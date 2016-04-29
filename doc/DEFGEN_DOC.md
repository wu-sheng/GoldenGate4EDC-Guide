#DEFDEN_DOC

###概述

* DEFGEN是GoldenGate的组件，用于生成.def文件
* .def文件用于描述源端需要被采集的表的数据
* 如果目标端没有适应最新数据库结构的.def文件，就无法正常工作，所以每次对表结构做修改，就要在源端生成新的.def文件


## 生成srcdef.def文件 ##

* 在源端GoldenGate目录下的dirprm目录里配置flatfile.prm文件，文件内容如下

	```shell

	--定义文件的输出路径：GoldenGate目录下dirdef里的srcdef.def文件，生成时要确保目录下没有同名文件
	DEFSFILE ./dirdef/srcdef.def
	--配置数据库的登录用户
	USERID goldengate,PASSWORD goldengate
	--配置文件需要解析的表
	TABLE test001.*;
	
	```
	
* 生成srcdef.def文件：在源端GoldenGate目录使用defgen生成该文件

	```shell
	$ ./defgen PARAMFILE ./dirprm/flatfile.prm
	```

* 将生成的srcdef.def文件拷贝到目标端的dirprm目录下，并重启读取文件的EXTRACT组件，笔者的EXTRACT组件名为ffwriter

	```shell
	GGSCI > STOP ffwriter
	GGSCI > START EXTRACT ffwriter
	```

