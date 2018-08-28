---
title: Hive表的几种操作 
tags: Hive,表操作,外部表,内部表,临时表,Sqoop
date: 2018-8-27 20:15:10
grammar_cjkRuby: true
---
前言：针对Hive的表，我基本会有如下的操作情况，汇总整理一下

 1. 创建表
	 * 创建Hive内部表
	 ```sql
	 CREATE TABLE test (
		customer_id   BIGINT,
		resource_id   BIGINT,
		resource_name STRING,
		resource_type INT,
		browse_time   BIGINT,
		browse_num    BIGINT
    )ROW FORMAT delimited FIELDS TERMINATED BY '\t';
	 ```
	 * 创建外部表
	 ```
	 create external table test(
		id string,
		customer_id string,
		illness_id string
		)
		STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler' 
		WITH SERDEPROPERTIES ("hbase.columns.mapping" = 
		":key,
		f:customer_id,
		f:illness_id
	")TBLPROPERTIES ("hbase.table.name"="customer_auth_illness"); 
	 ```
	 * 创建临时表
	 ```
	 CREATE TEMPORARY TABLE test (
	  doctor_id   BIGINT,
	  inquiry_all_num   BIGINT,
	  inquiry_accept_num   BIGINT,
	  acceptance_rate BIGINT
	)ROW FORMAT delimited FIELDS TERMINATED BY '\t';
	 ```

2. 清理表数据，临时表只对当前的session是有效的，所以当前的Hql运行完毕以后，改表自动被删除，所以不存在删除数据的情况
	* 内部表
	
	```
	truncate table test;
	```
	
	* 外部表,这里我说的是Hbase的外部表，因为对应的数据是保存在Hbase中，所以只能通过Hbase的命令来处理数据
	
	``` shell
	#!/bin/sh
	source /etc/profile

	echo "truncate 'recommend:test'" |hbase shell

	```
3. 导入数据
	* 从本地磁盘导入数据
	```
	// 增量导入
	load data local inpath '/data/test.txt' into table test;
	// 覆盖插入
	load data local inpath '/data/test.txt' OVERWRITE into table test;
	```
	* 从HDFS文件系统导入数据
 
 	```
	// 增量导入
	load data inpath '/data/test.txt' into table test;
	// 覆盖插入
	load data inpath '/data/test.txt' OVERWRITE into table test;
 	```
4. 导出数据
	* 导出到文件系统
	```
	insert overwrite local directory '/data/test.txt'  row format delimited  fields terminated by '\t' select t1 from test;
	```
   * 导出到数据库，使用的是Sqoop
   ```
   sqoop export --connect "jdbc:mysql://127.0.0.1:3306/test?useUnicode=true&characterEncoding=utf-8" --username root --password '123456' --table test  --columns "id, customer_id, customer_name" --export-dir /user/hive/warehouse/stat.db/test --input-fields-terminated-by '\t' --lines-terminated-by '\n' --input-null-string '\\N' --input-null-non-string '\\N' -m 1;

   ```