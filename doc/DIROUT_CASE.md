#DIROUT_CASE



#文件生成测试
### 用例概述
* INSERT操作，全量添加
* INSERT操作，部分添加
* UPDATE操作，以主键为条件，全量修改
* UPDATE操作，以主键为条件，部分修改
* UPDATE操作，以非主键为条件，全量修改
* UPDATE操作，以非主键为条件，部分修改
* DELETE操作，以主键为条件
* DELETE操作，以非主键为条件
* 所有修改必须要提交后方可生成输出文件


#测试表
* 建表语句

```
CREATE TABLE EMP2(ID NUMBER(8)  PRIMARY KEY,NAME VARCHAR2(14), DEPT VARCHAR2(14), POSI VARCHAR2(14));	
```


###INSERT操作，全量添加
 * SQL
 
```
INSERT INTO EMP2 (ID,NAME,DEPT,POSI) VALUES(1000,'name1000','dept1000','posi1000');
```

 * 文件名
 
```
pump_TEST001_EMP2_2016-04-29_09-46-44_00000_data.dsv
```
 
 * 输出结果
 
```
"3"|"I"|"2016-04-29 09:46:39.386989"|"TEST001"|"EMP2"|"ID"|<NULL>|1000|"NAME"|<NULL>|"name1000"|"DEPT"|<NULL>|"dept1000"|"POSI"|<NULL>|"posi1000"
```

###INSERT操作，部分添加

* SQL	
	
```
INSERT INTO EMP2 (ID,NAME,DEPT) VALUES(1001,'name1001','dept1001');
```

* 文件名  


```
pump_TEST001_EMP2_2016-04-29_09-46-44_00000_data.dsv
``` 

	 
* 输出结果	
	
```
"3"|"I"|"2016-04-29 09:48:14.387028"|"TEST001"|"EMP2"|"ID"|<NULL>|1001|"NAME"|<NULL>|"name1001"|"DEPT"|<NULL>|"dept1001"|"POSI"|<NULL>|<NULL>
```
	
###UPDATE操作，以主键为条件，全量修改

* SQL


```
UPDATE EMP2 SET NAME='name1002', DEPT='dept1002',POSI='posi1002' WHERE ID=1001;
```
		
* 文件名

```
pump_TEST001_EMP2_2016-04-29_10-02-39_00004_data.dsv
```
* 输出结果

```
"E"|"U"|"2016-04-29 10:02:34.386997"|"TEST001"|"EMP2"|"ID"|1001|1001|"NAME"|"name1001"|"name1002"|"DEPT"|"dept1001"|"dept1002"|"POSI"|<NULL>|"posi1002"
```
		
		
###UPDATE操作，以主键为条件，部分修改
		
* SQL

```
UPDATE EMP2 SET NAME='name1101', DEPT='dept1101' WHERE ID=1000;
```	
			
* 文件名

```
pump_TEST001_EMP2_2016-04-29_09-57-14_00003_data.dsv
```
			
* 输出结果
	
```
"E"|"U"|"2016-04-29 09:57:09.387067"|"TEST001"|"EMP2"|"ID"|1000|1000|"NAME"|"name1100"|"name1101"|"DEPT"|"dept1100"|"dept1101"|"POSI"||
```

###UPDATE操作，以非主键为条件，全量修改

* SQL

```
UPDATE EMP2 SET NAME='name1002', DEPT='dept1002', POSI='posi1003' WHERE NAME='name1002';
```			
		
* 文件名

```
pump_TEST001_EMP2_2016-04-29_10-06-10_00006_data.dsv
```

* 输出结果

```
"E"|"U"|"2016-04-29 10:06:06.386952"|"TEST001"|"EMP2"|"ID"|1001|1001|"NAME"|"name1002"|"name1002"|"DEPT"|"dept1002"|"dept1002"|"POSI"|"posi1002"|"posi1003"
```

###UPDATE部分修改，使用非主键作为条件

* SQL

```
UPDATE EMP2 SET NAME='name1102', DEPT='dept1102' WHERE NAME='name1101';
```
		
* 文件名

```
pump_TEST001_EMP2_2016-04-29_10-04-25_00005_data.dsv
```

* 输出结果

```
"E"|"U"|"2016-04-29 10:04:21.387062"|"TEST001"|"EMP2"|"ID"|1000|1000|"NAME"|"name1101"|"name1102"|"DEPT"|"dept1101"|"dept1102"|"POSI"||
```

###DELETE部分修改，使用主键作为条件

* SQL

```
DELETE FROM EMP2 WHERE ID=1000;	
```
		
* 文件名

```
pump_TEST001_EMP2_2016-04-29_10-08-40_00007_data.dsv
```
			
* 输出结果

```
"3"|"D"|"2016-04-29 10:08:34.387005"|"TEST001"|"EMP2"|"ID"|1000|<NULL>|"NAME"|||"DEPT"|||"POSI"||
```
				
			
###DELETE部分修改，使用非主键作为条件 
* SQL

```
DELETE FROM EMP2 WHERE name='name1002';
```

* 文件名

```
pump_TEST001_EMP2_2016-04-29_10-09-46_00008_data.dsv
```
			
* 输出结果

```
"3"|"D"|"2016-04-29 10:09:41.387059"|"TEST001"|"EMP2"|"ID"|1001|<NULL>|"NAME"|||"DEPT"|||"POSI"||
```
			
