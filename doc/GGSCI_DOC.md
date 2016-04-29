#GGSCI_DOC

#概述
- GGSCI（Oracle GoldenGate Software Command Interface）是GoldenGate提供的命令行接口，可以对GoldenGate进行配置，控制和管理

#组件相关
* Manager：GoldenGate的控制进程，该组件必须在Extract启动之前运行，并且在正常运行阶段不能关闭。它可以管理GoldenGate端口，管理其它组件。源端和目标端都有
* Extract：GoldenGate的数据抽取进程，该组件可以从数据源（通常是数据库或者数据库日志）抽取数据，并以特定形式输出，出现在源端
* Data Pumps：其本质也是EXTRACT，但DataPumps通常是将本地的Trails文件推送到远程的GoldenGate，这比直接用EXTRACT推送更加安全有效，出现在源端
* flatfilewriter：本质也是EXTRACT，将Trails文件解析后以文本的形式存储，需要配置用户接口和源端表结构，以及输出文件结构，出现在目标端

#组件状态
* STARTING：组件正在启动
* RUNNING：组件正在运行
* STOPPD：组件正常关闭
* ABENDED ：组件异常关闭，需要查看异常日志，以进一步分析

#查看异常
* 异常日志的位置在GoldenGate目录下，名为ggserr.log

#常用操作
* 启动GGSCI，在GoldenGate目录下运行GGSCI

```
$ ./ggsci
```
* 退出GGSCI

```
GGSCI > QUIT
```
* 查看GoldenGate状态
  * 使用show命令
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
  * 使用info命令
```
GGSIC > INFO ALL
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
GGSIC > INFO mgr
```
 * 笔者运行后结果如下
```
Manager is running (IP port h-7b6g4gqx.7999).
```
* 查看ext组件信息，出mgr之外，都需要带上组件类型，比如下面的EXTRACT，且组件必须存在
```
GGSIC > INFO EXTRACT ext
```
  * 笔者运行后结果如下
```
EXTRACT    EXT       Last Started 2016-04-29 14:31   Status RUNNING
Checkpoint Lag       00:00:00 (updated 00:00:03 ago)
Log Read Checkpoint  Oracle Redo Logs
                     2016-04-29 17:16:35  Seqno 89, RBA 18970624
                     SCN 0.1380378 (1380378)
```

* 初始化文件，建立GGSCI运行时需要的文件夹
```
GGSIC > CREATE SUBDIRS
```
 * 编辑组件,输入如下命令，编辑对应组件的配置文件，文件名为：<组件名>.prm
```
GGSIC > EDIT PARAMS mgr
```

* 添加组件，在编辑好prm文件后，将组件添加

* 添加EXTRACT, 以源端的ext为例子

* 将名为ext的EXTRACT加入，以便由GGSCI控制;TRANLOG 表示使用事务日志的方式;BEGIN NOW表示统计从吸纳在开始的事务

```shell
GGSCI > ADD EXTRACT ext, TRANLOG, BEGIN NOW
``` 
- 为ext指定输出文件，需要与配置文件里的EXTTRAIL字段一样

```shell
GGSCI > ADD  EXTTRAIL ./dirdat/la,EXTRACT ext
```

* 添加PUMP，以源端的pump为例子

* 添加pump，并添加本地源文件
```shell
GGSCI > ADD EXTRACT pump, EXTTRAILSOURCE ./dirdat/la
```
    
* 添加pump的目标文件，需要和配置文件中的RMTTRAILS字段一样

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
GGSCI > START EXTRACT EXTR
```
 * 关闭组件
```
GGSCI > STOP EXTR
```

