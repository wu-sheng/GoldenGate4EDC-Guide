#LOGDUMP_DOC

###概述
* logdump是GoldenGate下的一个小工具，可以用来检查Trails文件

###操作

* 启动logdump，在GoldenGate目录下

```
$ ./logdump
```
* 初始化
	* 查看trails文件头

	```
	
	Logdump > GHDR ON
	```
	* 查看列信息

	```
	Logdump > DETAIL ON		
	```
	
	* 查看八进制和ASCII信息

	```
	Logdump > DETAIL DATA
	```

	* 查看用户TOKENS

	```
	Logdump > USERTOKEN ON		
	```
	
	* 查看自动生成TOKENS
		
	```
	Logdump > GGSTOKEN ON
	```

		
	
###基本操作

* 打开文件

```
Logdump >open /aifs01/data/oggtest/dirdat/ra000000
```

* 遍历下一条记录

```
Logdump > N
```


* 移动到开头


```
Logdump > pos first
```

* 移动到结尾


```
Logdump > pos eof
```
* 转换游标方向

```
Logdump > pos rev
```
* 退出
	
```
Logdump > quit
```

#文件格式
* 头文件

```
2016/04/28 20:10:14.246.832 FileHeader           Len  1055 RBA 0 
Name: *FileHeader* 
3000 01c0 3000 0008 4747 0d0a 544c 0a0d 3100 0002 | 0...0...GG..TL..1...  
0003 3200 0004 2000 0000 3300 0008 02f2 57bd dd5e | ..2... ...3.....W..^  
c9b0 3400 001e 001c 7572 693a 682d 3762 3667 3467 | ..4.....uri:h-7b6g4g  
7178 3a3a 6461 7461 3a6f 6767 7465 7374 3500 0022 | qx::data:oggtest5.."  
3500 001e 001c 7572 693a 682d 3762 3667 3467 7178 | 5.....uri:h-7b6g4gqx  
3a3a 6461 7461 3a6f 6767 7465 7374 3600 0013 0011 | ::data:oggtest6.....  
2e2f 6469 7264 6174 2f72 6130 3030 3030 3037 0000 | ./dirdat/ra0000007..   
```
* 第二个文件

```
___________________________________________________________________ 
Hdr-Ind    :     E  (x45)     Partition  :     .  (x04)  
UndoFlag   :     .  (x00)     BeforeAfter:     A  (x41)  
RecLength  :    28  (x001c)   IO Time    : 2016/04/28 20:10:10.386.832   
IOType     :    15  (x0f)     OrigNode   :   255  (xff) 
TransInd   :     .  (x03)     FormatType :     R  (x52) 
SyskeyLen  :     0  (x00)     Incomplete :     .  (x00) 
AuditRBA   :         86       AuditPos   : 37497872 
Continued  :     N  (x00)     RecCount   :     1  (x01) 

2016/04/28 20:10:10.386.832 FieldComp            Len    28 RBA 1063 
Name: TEST001.DEMO1 
After  Image:                                             Partition 4   G  s   
 0000 000a 0000 0000 0000 0000 0003 0001 000a 0000 | ....................  
 0006 6e61 6d65 3333                               | ..name33  
Column     0 (x0000), Len    10 (x000a)  
 0000 0000 0000 0000 0003                          | ..........  
Column     1 (x0001), Len    10 (x000a)  
 0000 0006 6e61 6d65 3333                          | ....name33  
  
GGS tokens: 
 5200 0014 4141 4153 6853 4141 4541 4141 4144 3941 | R...AAAShSAAEAAAAD9A  
 4143 0001 4c00 0007 3133 3332 3831 3636 0000 0931 | AC..L...13328166...1  
 302e 322e 3230 3136                               | 0.2.2016  
```


###其他
* 使用的不是太多，所以参数就不详细解读了，可以参看下面的链接
* [Oracle文档](https://docs.oracle.com/goldengate/c1221/gg-winux/GLOGD/wu_logdump.htm#GLOGD112
)





