---
title: Hive外表的使用 
tags: hive,外联表,hbase,external
date: 2018-4-05 20:11:10
grammar_cjkRuby: true
---
前言：Hive创建关联hbase表有2种形式,第一是建立hive内表，指向Hbase，第二是建立Hive外表,引用Hbase中已经存在的一张表

 1. 在Hbase中创建表,创建一个表名称为`hive_hbase_test`,列族名称为`f`的Hbase表
	``` shell
	create 'hive_hbase_test','f'
	```
 2. 在Hive创建外联表，关联Hbase中对应的表
	``` shell
	create external table hive_hbase_test(
	id string,
	pageId string,
	operationParam string
	)
	STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler' 
	WITH SERDEPROPERTIES ("hbase.columns.mapping" = 
	":key,
	f:pageId,
	f:operationParam
	")TBLPROPERTIES ("hbase.table.name"="hive_hbase_test"); 
	```
 3. 外Hive表中插入数据，注意不能使用`load data`插入数据，不然后报`A non-native table cannot be used as target for LOAD`,需要使用`insert into`
	 
	``` shell
	insert into hive_hbase_test select id,page_id,operation_param from test_txt;
	```
 4. 查询HIVE中的数据
	  
	 ![enter description here][1]
	  
 5. 查询Hbase中的数据
  
   ![enter description here][2]
 6. 清理数据，不能使用hive的`truncate`数据，因为数据的存储是有Hbase来管理的，所以可以使用
  
  ![enter description here][3]

  ![enter description here][4]


  [1]: http://ov138d8j2.bkt.clouddn.com/2018/4/16/hive.png "hive数据"
  [2]: http://ov138d8j2.bkt.clouddn.com/2018/4/16/hbase.png "hbase数据"
  [3]: http://ov138d8j2.bkt.clouddn.com/2018/4/16/2018-04-16-1919.png "无法使用HIVE清理数据"
  [4]: http://ov138d8j2.bkt.clouddn.com/2018/4/16/2018-04-16_191953.png "HBase清理数据"