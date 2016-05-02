#GGSCI_DOC

#概述
- GGSCI（Oracle GoldenGate Software Command Interface）是GoldenGate提供的命令行接口，可以对GoldenGate进行配置，控制和管理

#GoldenGate组件相关
* Manager：GoldenGate的控制进程，该组件必须在Extract启动之前运行，并且在正常运行阶段不能关闭。它能管理GoldenGate对外开放的端口，以及其他组件。需要部署在源端和目标端
* Extract：GoldenGate的数据抽取进程，该组件可以从数据源（通常是数据库或者数据库日志）抽取数据，并以特定形式输出。部署在源端
* Pump：其本质是EXTRACT，但DataPumps通常是将本地的Trails文件推送到远程的GoldenGate，这比直接用EXTRACT推送更加安全高效。部署在源端
* FlatFileWriter：其本质是EXTRACT，它将Trails文件解析后以文本的形式输出，在工作之前需要配置用户接口和源端表结构，以及输出文件结构。部署在目标端

#组件状态
* STARTING：组件正在启动
* RUNNING：组件正在运行
* STOPPED：组件正常关闭
* ABENDED ：组件异常关闭，需要查看异常日志，以进一步分析

#查看异常
* 异常日志的位置在GoldenGate目录下，名为ggserr.log

#常用操作

* 启动GGSCI。进入GoldenGate目录下，输入以下命令后进入GGSCI

```
$ ./ggsci
```

* 退出GGSCI

```
GGSCI > QUIT
```

* 查看GoldenGate状态


```
GGSCI > SHOW  
```

* 笔者环境已经运行一段时间，结果如下：


```
Parameter settings:

SET SUBDIRS    ON
SET DEBUG      OFF

Current directory: /aifs01/data/oggtest

Using subdirectories for all process files

Editor:  vi

Reports (.rpt)                 /aifs01/data/oggtest/dirrpt
Parameters (.prm)              /aifs01/data/oggtest/dirprm
Stdout (.out)                  /aifs01/data/oggtest/dirout
Replicat Checkpoints (.cpr)    /aifs01/data/oggtest/dirchk
Extract Checkpoints (.cpe)     /aifs01/data/oggtest/dirchk
Process Status (.pcs)          /aifs01/data/oggtest/dirpcs
SQL Scripts (.sql)             /aifs01/data/oggtest/dirsql
Database Definitions (.def)    /aifs01/data/oggtest/dirdef

```

* 查看信息

```
GGSCI > INFO ALL
```
* 笔者环境已经运行一段时间，结果如下

```
Program     Status      Group       Lag at Chkpt  Time Since Chkpt

MANAGER     RUNNING                                           
EXTRACT     RUNNING     EXT         00:00:00      00:00:01    
EXTRACT     RUNNING     PUMP        00:00:00      00:00:04    

```

* 查看Manager组件信息

```
GGSCI > INFO mgr
```

* 笔者运行后结果如下

```
Manager is running (IP port h-7b6g4gqx.7999).
```

* 查看ext组件信息，出mgr之外，都需要带上组件类型，比如下面的EXTRACT，如果组件存在，则会出现相关信息


```
GGSCI > INFO EXTRACT ext
```

* 笔者运行后结果如下


```
EXTRACT    EXT       Last Started 2016-04-29 14:31   Status RUNNING
Checkpoint Lag       00:00:00 (updated 00:00:03 ago)
Log Read Checkpoint  Oracle Redo Logs
                     2016-04-29 17:16:35  Seqno 89, RBA 18970624
                     SCN 0.1380378 (1380378)
```

* 初始化文件，建立GGSCI运行时需要的文件夹，具体的文件在上面关于SHOW命令的说明已经有提示


```
GGSCI > CREATE SUBDIRS
```

* 登录数据库，是部分命令执行的条件，比如删除EXTRACT组件。需要使用为GoldenGate开通的Oracle账户，下面是笔者使用的命令


```
GGSCI > DBLOGIN USERID goldengate, PASSWORD goldengate
```

* 添加对指定表的事务监视，笔者添加的是test001用户下的所有表

```
GGSCI > ADD TRANDATA test001.*
```

* 如果想添加特定的表，可以用下面的语句，笔者尝试添加test001用户下的emp表

```
GGSCI > ADD TRANDATA test001.emp
``` 

* 编辑组件,输入如下命令，编辑对应组件的配置文件，文件名为：<组件名>.prm。各组件具体的配置已经在readme.md中展示，这里不再叙述。


```
GGSCI > EDIT PARAMS mgr
```

* 添加组件，在编辑好prm文件后，将组件添加

* 添加EXTRACT, 以源端的ext为例子

 * 将名为ext的EXTRACT加入，以便由GGSCI控制;TRANLOG 表示使用事务日志的方式;BEGIN NOW表示统计从现在开始的事务
 
 ```shell
 GGSCI > ADD EXTRACT ext, TRANLOG, BEGIN NOW
 ``` 
 * 为ext指定输出文件，需要与配置文件里的EXTTRAIL字段一样

 ```shell
 GGSCI > ADD  EXTTRAIL ./dirdat/la,EXTRACT ext
 ```

* 添加Pump，以源端的pump为例子

 * 添加pump，并添加本地源文件，EXTTRAILSOURCE表示本地Trails文件的位置
 
 ```shell
 GGSCI > ADD EXTRACT pump, EXTTRAILSOURCE ./dirdat/la
 ```
    
 * 添加pump的目标文件，需要和pump的配置文件中的RMTTRAILS字段一样
 
 ```shell
 GGSCI > ADD RMTTRAIL ./dirdat/ra, EXTRACT pump
 ```

* 添加FLATWRITER，以目标端的ffwriter为例
 * 添加ffwriter并设定输入路径
 
 ```shell
 GGSCI > ADD EXTRACT ffwriter, EXTTRAILSOURCE ./dirdat/ra
 ```

* 启动组件，启动后最好查询状态看是否启动成功，对mgr可以直接使用:START MGR
```
GGSCI > START EXTRACT ext
```
* 关闭组件

```
GGSCI > STOP ext
```
* 删除组件，需要使用DBLOGIN命令登陆数据库

```
 GGSCI > DELETE EXTRACT ext
```


