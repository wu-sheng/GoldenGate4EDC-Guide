
# GoldenGate4EDC-Guide #

# 概述 #

## GoldenGate简介 ##
- GoldenGate是Oracle公司的一款数据库中间件，它基于数据库的二进制日志以及事务提交，实现了数据库集群之间的数据迁移，主要用于数据库之间的同步和备份
- 我们用GoldenGate来实现了对数据库数据修改的采集，以及将这些变化在远端输出为可供程序读取的文本文件

## 结构简介 ##
- GoldenGate分为两端
- 源端作为数据源，负责从数据库采集数据，生成Trails文件，并发送到目标端
- 目标端作为终点，负责处理从源端传来的Trails文件，并将这些文件转换为文本文件
- 结构图
### 源端 ###
- 源端上装有Oracle和GoldenGate，GoldenGate将Oracle的数据采集并以事务为单位，写入本地Trails文件，并在远程的GoldenGate上生成新的Trails文件（目前只有DML操作）
- manager：GoldenGate的监控进程，负责控制GoldenGate的其他组件
- extract：从数据库的二进制日志中抽取变化到Trails文件
- pump：从源端的Trails文件里抽取信息并推送到目标端的Trails文件
### 目标端 ###
- 目标端上的GoldenGate将源端输送过来的GoldenGate文件以特定的规则，转换为特定格式的文本文件
- manager：GoldenGate的监控进程，负责控制GoldenGate的其他组件
- ffwriter：将本地的Trails文件读取并转换为文本文件
### 其他组件 ###
- 组件全都位于GoldenGate的根目录下
- ggsci：GoldenGate的控制台，可以通过这个操作GoldenGate的附属组件
- defgen：用于生成srcdef文件，供目标端的GoldenGate使用
- logdump：用来解析Trails文件

# 环境配置 #
## Oracle设置 ##
- 源端的主机上需要装有数据库，笔者使用的Oracle11g，安装Oracle的过程就不再叙述
- 开启归档模式

    `SQL>alter database archivelog`
- 开启补充日志

    `SQL>alter database add supplement log data;`
- 创建一个用于GoldenGate的账户，需要DBA权限
	
## 源端 ##
- 初始化GGSCI

    `$ ./ggsci`
	`GGSCI > CREATE SUBDIRS`
- 添加需要被采集的表，笔者需要采集test001用户下所有的表

	`GGSCI > DBLOGIN USERID goldengate, PASSWORD goldengate`
	`GGSCI > add trandata test001.*`
- 配置Manager

    `GGSCI > edit params mgr`
	
- mgr.prm

    `port 7999`
- 启动mgr

    `GGSCI > START mgr`
- 配置extract

    `EDIT PARAMS etc`
- 配置文件ext.prm

	```
	EXTRACT ext
	SETENV (ORACLE_SID = "sid")	
	SETENV (NLS_LANG = "AMERICAN_AMERICA.ZHS16GBK")	
	USERID userid, PASSWORD password	
	EXTTRAIL ./dirdat/la	
	dynamicresolution	
	table test001.*;
	```

- 添加extract

    `ADD EXTRACT ext, TRANLOG, BEGIN NOW`
- 并指定输出文件

    `ADD  EXTTRAIL ./dirdat/la,EXTRACT ext`
- 启动ext

    `START EXTRACT ext`
- 配置pump

    `EDIT PARAMS pump`
- 配置文件pump.prm

    ```
	extract pump
	passthru	
	rmthost 10.1.130.240, mgrport 7999	
	rmttrail ./dirdat/ra	
	dynamicresolution,nocompressupdates	
	table test001.*;

	```
- 添加pump，并添加本地源文件

    `ADD EXTRACT pump, EXTTRAILSOURCE ./dirdat/la`
- 添加pump的目标文件

	`ADD RMTTRAIL ./dirdat/ra, EXTRACT pump`

- 启动

    `START EXTRACT pump`
## 目标端 ##
- 将lib添加到根目录
- 初始化ggsci

    `GGSCI > CREATE SUBDIRS`
- 配置manager

	`EDIT PARAMS mgr`
- 配置文件内容如下

    `port 7999`
- 启动

    `START mgr`
- 配置ffwriter

    `EDIT PARAMS FFWRITER`
- 配置文件ffwriter.prm

    ```
	extract ffwriter

	sourcedefs ./dirdef/srcdef.def
	
	CUSEREXIT ./flatfilewriter.so CUSEREXIT PASSTHRU INCLUDEUPDATEBEFORES,PARAMS "./dirprm/ffwriter.properties"
	
	table test001.*;	

	```
- 添加ffwriter并设定输入路径

    `ADD EXTRACT ffwriter, EXTTRAILSOURCE ./dirdat/ra`
- 复制ffwriter.properties文件到dirprm目录下
- 启动

    `START EXTRACT ffwriter`

## 注意 ##
- 如果提示缺少文件，可以从lib中找到，放到GoldenGate的Home目录下即可

# 准备工作 #
## 检验GoldenGate状态是否正确 ##
- 在ggsci中查看各组件的状态,均为RUNNING表示健康
## 重新生成srcdef.def文件 ##
- 在源端配置flatfile.prm文件

    ```
	DEFSFILE ./dirdef/srcdef.def
	USERID goldengate,PASSWORD goldengate
	TABLE test001.*;	
	```
- 在源端生成srcdef.def文件：在GoldenGate目录使用defgen生成该文件

    `$ ./defgen PARAMFILE ./dirprm/flatfile.prm`
- 将生成的srcdef.def文件拷贝到目标端的dirprm目录下


# 生成文件格式说明 #
## 文件名 ##
- 格式：前缀\_用户名\_表名\_年\_月\_日\_时\_分\_秒\_编号\_后缀
- 每个表会有一组文件来记录其改变
- 如果有持续的事务提交，每10秒会生成一个新的文件[需要看配置文件]
- 文件的编号从00000开始递增
- 文件名可配置，在ffwriter.properties文件中配置
## 文件内容 ##
- 格式："事务类型"|" DML操作类型"|"时间"|"用户名""表名"|"K"|old V|new V[|"K"|old V|new V]...
- 事务类型
- DML操作类型
- 在不同操作时KV的呈现

# 配置文件格式说明 #
输出配置文件ffwriter.properties，请见github


