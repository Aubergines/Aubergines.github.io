---
title: Hadoop学习
notebook: 大数据学习
tags: [大数据，Hadoop]
grammar_cjkRuby: true
---
## 前言
Hadoop是使用Java编写，允许分布在集群，使用简单的编程模型的计算机大型数据集处理的Apache的开源框架。 Hadoop框架应用工程提供跨计算机集群的分布式存储和计算的环境。 Hadoop是专为从单一服务器到上千台机器扩展，每个机器都可以提供本地计算和存储

## Hadoop的安装
	
 1. 安装JDK：因为Hadoop是使用Java编写的，所以Hadoop的运行环境是依赖JDK，关于JDK的安装，请看之前编写的文章 [ JDK的安装](http://www.aubergine.cc/2017/08/15/Centos%20%E5%AE%89%E8%A3%85%E4%B8%8E%E9%85%8D%E7%BD%AEJava/)，或者自行网上搜索安装教程。
 
 2. Hadoop的安装

	 1. 去[Hadoop官网](http://hadoop.apache.org/)，请选择稳定的版本下载，这是Apache建议国内的下载的地址，所有的Hadoop发布的版本[目录](http://mirror.bit.edu.cn/apache/hadoop/common/)，选择合适的下载。
	 
	 2. 解压缩当前的文件
		 1.  移动下载的文件到安装的目录：mv hadoop-2.7.4.tar.gz /usr/
		 2.  进入到对应的目录，解压当前的文件：cd /usr/  ;  tar -xzvf hadoop-2.7.4.tar.gz

	3. 创建符号链接： ln -s /usr/hadoop-2.7.4 /usr/hadoop
	4. 配置环境变量，编辑` vim /etc/profile` ，添加如下的内容
	
		> export HADOOP_HOME=/usr/hadoop
		export PATH=$PATH:$HADOOP_HOME/bin:$HADOOP_HOME/sbin
		
		
	5. 验证Hadoop是否安装成功：` ./usr/hadoop/bin/hadoop version ` ，显示如下的信息，代表Hadoop安装成功了。
	
	 ![enter description here][1]


## Hadoop的配置

　Hadoop有三种运行模式，分别是：Standalone (or local) mode（单机或者本地），Pseudodistributed mode（伪分布式），Fully distributed mode（全分布式）。
 	1. 单机模式，不需要做任何的操作
 	2. 伪分布模式:

 1. 进入需要修改配置的目录：`/usr/hadoop/etc/hadoop`
 2. 修改配置文件:`core-site.xml`,`hdfs-site.xml`,`mapred-site.xml`,`yarn-site.xml`
	 1. 编辑core-site.xml
		```
		 <?xml version="1.0"?>
		<configuration>
			<property>
				<name>fs.defaultFS</name>
				<value>hdfs://localhost/</value>
			</property>
		</configuration>
		```
	2. 编辑hdfs-site.xml
		```
		<?xml version="1.0"?>
		<configuration>
			<property>
				<name>dfs.replication</name>
				<value>1</value>
			</property>
		</configuration>
		```
	  3. 编辑mapred-site.xml,注意，只有mapred-site.xml.template，所以要使用`cp mapred-site.xml.template mapred-site.xml` 复制一份文件
			```
			<?xml version="1.0"?>
			<configuration>
				<property>
					<name>mapreduce.framework.name</name>
					<value>yarn</value>
				</property>
			</configuration>
			```
	 4. 编辑yarn-site.xml
		```
		<?xml version="1.0"?>
		<configuration>
			<property>
				<name>yarn.resourcemanager.hostname</name>
				<value>localhost</value>
			</property>
			<property>
				<name>yarn.nodemanager.aux-services</name>
				<value>mapreduce_shuffle</value>
			</property>
		</configuration>
		```

		![enter description here][3]
		
		![enter description here][4]
		


  [1]: http://ov138d8j2.bkt.clouddn.com/2017/9/2/2017-09-02_134220.png "Hadoop 版本信息"
  [2]: http://ov138d8j2.bkt.clouddn.com/2017/9/19/2017-09-15_222231.png "进程信息"
  [3]: http://ov138d8j2.bkt.clouddn.com/2017/9/19/2017-09-19_223410.png "进程信息"
  [4]: http://ov138d8j2.bkt.clouddn.com/2017/9/19/2017-09-19_223424.png "详细步骤"