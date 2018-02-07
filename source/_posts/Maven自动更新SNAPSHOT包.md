---
title: Maven自动更新SNAPSHOT包 
tags: Maven、SNAPSHOT
grammar_cjkRuby: true
---

标签： [Maven, 自动更新]

1. IDEA配置修改：勾选“Always update snapshots”

    ![勾选](http://ov138d8j2.bkt.clouddn.com/2018/2/7/2018-02-07_135759.png)

2. Maven配置修改：${MAVEN_HOME}/conf/settings.xml配置文件修改

    1. <profiles></profiles> 节点下添加如下内容
        ```
        <profile>
          <id>central</id>
          <repositories>
              <repository>
                  <id>Central</id>
                  <name>Central</name>
                  <url>http://repo1.maven.org/maven2/</url>
                  <releases>
                      <enabled>true</enabled>
                  </releases>
                  <snapshots>
                      <enabled>true</enabled>
                  </snapshots>
              </repository>
          </repositories>
        </profile>
        <profile>
          <id>nexus</id>
          <repositories>
              <repository>
                  <id>nexus</id>
                  <name>allin</name>
                  <url>http://192.168.1.138:8081/repository/maven-public/</url>
                  <releases>
                      <enabled>true</enabled>
                  </releases>
                  <snapshots>
                      <enabled>true</enabled>
                  </snapshots>
              </repository>
          </repositories>
        </profile>
        
        ```
    2. <settings></settings>节点下添加如下内容
        ```
        <activeProfiles>
            <activeProfile>central</activeProfile>
            <activeProfile>nexus</activeProfile>
        </activeProfiles>
        ```
3. 有新的JAR，右键pom.xml → maven → Reimport,检查对应的JAR包是否修改，如下图`20190207.055230-3`属于小版本号，这个版本有变化，表示更新成功
    
     ![JAR包](http://ov138d8j2.bkt.clouddn.com/2018/2/7/2018-02-07_140520.png)

4. 如果此时更新不成功，检查IDEA的maven的配置是否正确，或者删除`~\.m2\repository`下当前的JAR包的目录，重启IDEA