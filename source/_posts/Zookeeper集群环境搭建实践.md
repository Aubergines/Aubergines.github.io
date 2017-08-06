---
title: Zookeeper集群环境搭建实践
---
# Zookeeper集群环境搭建实践

标签（空格分隔）： zookeeper

---

 1. 使用三台服务器模拟搭建部署ZooKeeper集群，集群机器IP如下：
 > 192.168.1.151
 > 192.168.1.152
 > 192.168.1.152

 2. 下载Zookeeper:
  #### [zookeeper官网点击下载](http://www.apache.org/dyn/closer.cgi/zookeeper/)
 3. 安装Zookeeper：
    解压下载的压缩包到 `/usr/zookeeper` 命令：`tar -zxvf *.tar`
 4. 配置将conf/zoo_sample.cfg拷贝一份命名为zoo.cfg，也放在conf目录下。然后按照如下值修改其中的配置：
> dataDir=/var/lib/zookeeper
> tickTime=2000
> initLimit=5
> syncLimit=2
> clientPort=2181
> server.0=192.168.1.151:2888:3888
> server.1=192.168.1.152:2888:3888
> server.2=192.168.1.153:2888:3888

 5. 创建/var/lib/zookeeper快照目录，并创建server id文件，在该目录下创建名为myid的文件，内容为1
 6. 将当前机器上已经配置好的`/usr/zookeeper`和`/var/lib/zookeeper`两个目录分别拷贝至152和153。然后将其上的/var/lib/zookeeper/myid的内容修改为2和3.
 7. 执行运行脚本与查看状态,通过查看`status`可以看到当前的`zookeeper`的服务是follower还是leader
> ./zkServer.sh start
./zkServer.sh status