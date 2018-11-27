---
title: CentOS 7安装配置Redis数据库
date: 2017-8-20 20:12:10
tags: [Centos, Redis]
---
# CentOS 7安装配置Redis数据库

标签： [Centos, Redis]

---

Redis就是一种基于key-value的非关系型数据库。这里我将介绍Redis在CentOS 7下如何安装配置

**方法/步骤**

**Redis源码获取**

1.1、进入Redis官网获取Redis最新稳定版下载地址2、通过wget命令下载 Redis 源代码。

![Redis源码](http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/20170824/20170824001.png)

**Redis编译

* * *

1、通过tar -xvf redis-3.0.2.tar.gz命令解压下载Redis源码压缩包redis-3.0.2.tar.gz；

2、编译Redis。通过cd redis-3.0.2/进入Redis源码目录内，执行make编译Redis；注意：

make命令执行完成编译后，会在src目录下生成6个可执行文件，分别是redis-server、redis-cli、redis-benchmark、redis-check-aof、redis-check-dump、redis-sentinel

![确认文件](http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/20170824/20170824002.png)


**Redis安装配置**

1、安装Redis，执行make install。会将ll编译生成的可执行文件拷贝到/usr/local/bin目录下；

2、执行./utils/install_server.sh配置Redis配置之后Redis能随系统启动。

![make install](http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/20170824/20170824003.png)

![增加开机启动](http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/20170824/20170824004.png)

**Redis服务查看、开启、关闭**

1、通过ps -ef|grep redis命令查看Redis进程；

2、开启Redis服务操作通过/etc/inisert.d/redis_6379 start命令，也可通过（service redis_6379 start）；

3、关闭Redis服务操作通过/etc/init.d/redis_6379 stop命令，也可通过（service redis_6379 stop）；

![查看启动情况](http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/20170824/20170824005.png)

**Redis的测试与使用**

** ** 开启Redis服务，进入Redis的命令行，近行测试

![redis 测试](http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/20170824/20170824006.png)

注：

在编译安装Redis的时候在执行Make后一般会要求有一个“Make Test”的测试检查，如果你的机器没有安装 Tcl的话会报出如下的异常，这样我们就可以测试了

You need tcl 8.5 or newer in order to run the Redis test

【安装Tcl-8.6.3】

[root@localhost202 src]# wget http://downloads.sourceforge.net/tcl/tcl8.6.3-src.tar.gz

[root@localhost202 src]# tar -zxf tcl8.6.3-src.tar.gz

[root@localhost202 src]# cd tcl8.6.3/unix/

[root@localhost202 src]# ./configure

[root@localhost202 src]# make

[root@localhost202 src]# make install

**Redis的安全配置** * 当我们编译安装完redis后，并且设置完redis随机器启动的时候，我们的配置文件实际上已经自动生成的位置
    
    
    当作为系统服务的时候redis的，启动文件的配置：/etc/init.d/redis_6379
    redis的配置文件的位置，默认的文件的名称：/etc/init.d/redis_6379
    

如果想要修改redis的配置，我们不能修改源码中的redis的redis.cof这个文件

  * 修改的配置文件的内容
  *     * /etc/init.d/redis_6379 通过启动脚本，我们可以确定，redis的配置文件的位置
    * /etc/init.d/redis_6379修改的参数
    *       * 开放当前的bind，添加本机外网的地址 bind 127.0.0.1 115.28.87.77
      * 修改requirepass，开放，添加author：requirepass aubergine
  * 用户的登录
  *     * redis-cli -a aubergine -a 表示auth
    * redis-cli --help 帮助文档





