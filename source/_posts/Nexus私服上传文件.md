---
title: Nexus私服上传文件 
tags: nexus,pom,maven
date: 2019-2-20 14:39:20
categories: 分类
grammar_cjkRuby: true
---

##### 前言：Nuxus从3.0以后上传Jar、POM就不能通过网页手动上传了，只能通过Maven、Ant、Gradle、Docker CLI等命令的方式上传对应的文件，如下是使用`Maven`上传文件的几种操作

1. 没有pom文件的示例
```shell
mvn deploy:deploy-file -DgroupId=com.somecompany -DartifactId=project -Dversion=1.0.0 -DgeneratePom=true -Dpackaging=jar -DrepositoryId=nexus -Durl=http://localhost:8081/repository/maven-releases -Dfile=target/project-1.0.0.jar
```

2. 带有POM文件的示例
```shell
mvn deploy:deploy-file -DgeneratePom=false -DrepositoryId=nexus -Durl=http://localhost:8081/repository/maven-releases -DpomFile=pom.xml -Dfile=target/project-1.0.0.jar
```

3. 此插件目标还允许部署快照版本，插件将负责计算所需的时间戳版本
```shell
mvn deploy:deploy-file -DgroupId=com.somecompany -DartifactId=project -Dversion=1.0.0-SNAPSHOT -DgeneratePom=true -Dpackaging=jar -DrepositoryId=nexus -Durl=http://localhost:8081/repository/maven-snapshots -Dfile=target/project-1.0.0-SNAPSHOT.jar
```
4. 如果是为了上传POM文件

```shell
mvn deploy:deploy-file -DgroupId=com.allin.bd -DartifactId=trans-parent -Dversion=1.0.1-SNAPSHOT -Dpackaging=pom -Dfile=E:\pom.xml -Durl=http://localhost:8081/repository/maven-snapshots/ -DrepositoryId=nexus

```

PS: `-DrepositoryId=nexus`中的`nexus`这个是在`Maven`的配置文件`settings.xml`中配置的仓库信息,对应的`username`,`password`是登录私服的账号和密码

```xml
<servers>
  <server>
      <id>nexus</id>
      <username>deployment</username>
      <password>deployment123</password>
    </server>
</servers>
```