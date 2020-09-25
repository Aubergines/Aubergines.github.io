---
title: Spring-Cloud搭建Eureka高可用集群
tags: spring cloud,eureka,高可用
date: 2018-12-23 21:9:43
categories: spring cloud
grammar_cjkRuby: true
---
#### 最近开始系统的学习spring boot 2.0的相关的知识，今天来看搭建eureka的搭建

1. pom.xml文件的配置,之前使用的是`spring-cloud-starter-eureka-server`,在启动项目的时候提示这个的状态是`deprecated`的，建议使用`spring-cloud-starter-netflix-eureka-server`,这里我使用的是最新版的`2.0.1.RELEASE`

``` xml
   <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.1.1.RELEASE</version>
    </parent>
	
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>
        <spring-cloud.version>Greenwich.RC2</spring-cloud.version>
    </properties>
    <dependencies>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
            <version>2.0.1.RELEASE</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
    </dependencies>
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>
```

2. application.yml的配置,这里我做的是两台机器的，另外一个的配置只要修改`hostname`为`peer2`，`defaultZone`为`http://admin:123456@peer1:8002/eureka/`即可

``` yml
server:
  port: 8001
eureka:
  instance:
    hostname: peer1
  client:
    fetch-registry: false
    serviceUrl:
      defaultZone: http://admin:123456@peer2:8002/eureka/
    register-with-eureka: false
spring:
  application:
    name: eureka-server
  security:
    user:
      name: admin
      password: 123456
```

3. 启动类的配置,不知道为什么当我把`EnableEurekaServer`换成`EnableDiscoveryClient`的时候，一个服务起来没有问题，另外一个在访问eureka的时候始终是404

``` java
@EnableEurekaServer
@SpringBootApplication
public class Application {
	public static void main(String[] args) {
		SpringApplication.run(Application.class, args);
	}
}
```

4. 启动启动类，查看eureka的监控页面，如果你的出现我这样的监控页面，恭喜你成功了

![监控页面](http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/2018/12/20181223003.png)
