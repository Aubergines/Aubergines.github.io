---
title: Spring Cloud Config配置中心 
tags: config,配置中心
date: 2019-8-7 23:41:53
categories: spring cloud
grammar_cjkRuby: true
---

1. pom文件

```xml
	<parent>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-parent</artifactId>
		<version>1.5.4.RELEASE</version>
		<relativePath/>
	</parent>
	<properties>
		<project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
		<java.version>1.8</java.version>
	</properties>
	<dependencies>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-config-server</artifactId>
		</dependency>
		<dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
        </dependency>
	</dependencies>

	<dependencyManagement>
		<dependencies>
			<dependency>
				<groupId>org.springframework.cloud</groupId>
				<artifactId>spring-cloud-dependencies</artifactId>
				<version>Dalston.SR1</version>
				<type>pom</type>
				<scope>import</scope>
			</dependency>
		</dependencies>
	</dependencyManagement>
```

2. 启动类

```java
@EnableConfigServer
@SpringBootApplication
@EnableDiscoveryClient
public class Application {
	public static void main(String[] args) {
		new SpringApplicationBuilder(Application.class).web(true).run(args);
	}
}
```

3. yml 配置

```yml
spring:
  application:
    name: config-server
  cloud:
    config:
      server:
        git:
          uri: http://192.168.1.1/cloud/config_files.git
         username: abc
		 password: abc
server:
  port: 1201
eureka: 
	client:
		serviceUrl:
			defaultZone: http://root:123456@192.168.1.1:1202/eureka/
```
4. 注意点
在GIT中的配置文件是以{nam}-{profiles}.yml形式的文件名称，一般name表示配置文件对应的application的名称，profiles表示对应的环境，我们一般会有abc-local.yml文件，abc-test.yml，abc-online.yml文件用来区分对应的环境。还有一种是/{lable}/{nam}-{profiles}.yml区分环境配置，其中lable表示git项目的分支，通过不同的分支区分不同的环境。



