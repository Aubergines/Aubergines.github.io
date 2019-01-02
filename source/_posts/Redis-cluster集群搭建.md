---
title: Redis-cluster集群搭建 
tags: redis,redis cluster,集群,搭建
date: 2018-12-30 17:1:46
categories: 运维
grammar_cjkRuby: true
---


1. 下载稳定的源码包
	`wget http://download.redis.io/releases/redis-5.0.3.tar.gz`
2. 解压包到`/usr/local/src`下面
	`tar -zxvf redis-5.0.3.tar.gz -C /usr/local/src/`
3. make install
4. 创建集群的配置文件目录
	* ` mkdir -p /usr/local/redis-cluster`
	* ` mkdir 16001 16002 16003 16004 16005 16006`
4. 配置文件  `/etc/redis/redis.config`
	* `protected-mode yes`:是为了禁止公网访问redis cache，加强redis安全的
	* `port 16001`
	* `daemonize yes` 后台运行
	* `pidfile /var/run/redis_16001.pid`
	* `logfile "/usr/local/redis-cluster/16001/redis.log"`
	* `requirepass admin`
	* `appendonly yes 与 appendfilename "appendonly.aof"`
	* `appendfsync everysec`
	* `cluster-enabled yes`
	* `cluster-config-file nodes-16001.conf`
5. 启动所有的节点
	 `/usr/local/src/redis-5.0.3/src/redis-server ./16001/redis.conf`
6. 检查节点的启动的情况
	ps -aux | grep redis
7. 配置节点
	`redis-cli -a aubergine --cluster create 127.0.0.1:16001 127.0.0.1:16002  127.0.0.1:16003  127.0.0.1:16004  127.0.0.1:16005  127.0.0.1:16006 --cluster-replicas 1`
	出现提示分配solts的时候输入yes就可以了
8. 连接节点测试
	` /usr/local/src/redis-5.0.3/src/redis-cli -c -h 127.0.0.1 -p 16001`