---
title: Centos 安装与配置Java
date: 2017-8-15 12 22:12:10
tags: Centos、 Java、 JDK
---
# Centos 安装与配置Java

标签（空格分隔）： Centos Java JDK

---
我的环境是`CentOS release 6.5`，选择安装的JDK版本是`1.8.0_77`,下面是我的安装的过程：

 1. 下载JDK,一般Linux可以使用的版本是rpm或者tar.gz两种文件。
    ![Oracle官网的信息](https://thumbnail0.baidupcs.com/thumbnail/de4e6b59bc4f306cd8070fd50ee58732?fid=286293584-250528-227731002301805&time=1502787600&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-pOV1rnEYsHe8RYYKm2jj3srlVts%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=5258302600596834627&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)
    获取[Oracle官网获取文件的地址](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)
    也可以使用wget方法获取文件: `wget http://download.oracle.com/otn-pub/java/jdk/8u144-b01/090f390dda5b47b9b721c7dfaa008135/jdk-8u144-linux-x64.tar.gz`
 
 2. 选择要安装java的位置，如/usr/目录下，新建文件夹java(mkdir java)
 
 3. 将文件*tar.gz移动到/usr/java
 
 4. 解压：tar -zxvf *.tar.gz
 
 5. 删除 *.tar.gz（为了节省空间）
 
 6. 打开/etc/profile（vim /etc/profile），在最后面添加如下内容：

    > `JAVA_HOME=/usr/java/jdk1.7.0_40`
    > `CLASSPATH=.:$JAVA_HOME/lib.tools.jar`
    > `PATH=$JAVA_HOME/bin:$PATH`
    > `export JAVA_HOME CLASSPATH PATH`

    至此，jkd安装完毕，下面配置环境变量

 7. source /etc/profile

 8. 验证是否安装成功：java -version
    ![Java Version的版本信息](https://thumbnail0.baidupcs.com/thumbnail/bfe0b4d88511c19caadd84b489454c8b?fid=286293584-250528-1056413199211259&time=1502787600&rt=sh&sign=FDTAER-DCb740ccc5511e5e8fedcff06b081203-TRzrMYra0wy3ENpKJfF0n0WB%2Fy4%3D&expires=8h&chkv=0&chkbd=0&chkpc=&dp-logid=5258319068848520264&dp-callid=0&size=c710_u400&quality=100&vuk=-&ft=video)




