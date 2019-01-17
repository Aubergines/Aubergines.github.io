---
title: Spring-Cloud集成Redisson
tags: Spring-Cloud,redisson,redis
date: 2019-1-17 11:53:1
categories: spring cloud
grammar_cjkRuby: true
---
#### 前言：目前官方罗列的连接redis的Java客户端虽然有六种最近六个月在更新，不过推荐的只有`Jedis`,`lettuce`,`Redisson`,一般用的比较多的是`Jedis`,不过相对来说`Redisson`的功能更强大一些，今天尝试了基于spring-cloud集成`Redisson`

1. pom文件，我使用的是`redisson`的spring boot的starter`redisson-spring-boot-starter`,目前的版本是`3.9.1`

``` xml
<dependencies>
	<dependency>
		<groupId>org.redisson</groupId>
		<artifactId>redisson-spring-boot-starter</artifactId>
		<version>3.9.1</version>
	</dependency>
	<dependency>
            <groupId>org.redisson</groupId>
            <artifactId>redisson-spring-data-21</artifactId>
            <version>3.10.0</version>
     </dependency>
	<dependency>
		<groupId>org.springframework.cloud</groupId>
		<artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
		<version>2.0.1.RELEASE</version>
	</dependency>
</dependencies>
```
2. 添加配置到配置文件中`application.yml`,官方提供了两种方式，第一种是新建一个配置文件，添加连接Redis的配置，然后把对应的配置文件在`application.yml`引用，例如`spring.redis.redisson.config=classpath:redisson.yaml`,就是引用`Redisson.yaml`中的配置，第二种就是直接在`application.yml`配置

``` yml
server:
  port: 8003
spring:
  application:
    name: redis-server
  redis:
    cluster:
      nodes: 192.168.1.154:6379,192.168.1.154:6380,192.168.1.155:6379,192.168.1.155:6380,192.168.1.156:6379,192.168.1.156:6380
    password: admin
```
或者使用外置配置文件的方法

``` yaml
clusterServersConfig:
  idleConnectionTimeout: 10000
  pingTimeout: 1000
  connectTimeout: 10000
  timeout: 3000
  retryAttempts: 3
  retryInterval: 1500
  reconnectionTimeout: 3000
  failedAttempts: 3
  password: admin
  subscriptionsPerConnection: 5
  clientName: "cluster"
  loadBalancer: !<org.redisson.connection.balancer.RoundRobinLoadBalancer> {}
  slaveSubscriptionConnectionMinimumIdleSize: 1
  slaveSubscriptionConnectionPoolSize: 50
  slaveConnectionMinimumIdleSize: 32
  slaveConnectionPoolSize: 64
  masterConnectionMinimumIdleSize: 32
  masterConnectionPoolSize: 64
  readMode: "SLAVE"
  nodeAddresses:
  - "redis://192.168.1.154:6379"
  - "redis://192.168.1.154:6380"
  - "redis://192.168.1.155:6379"
  - "redis://192.168.1.155:6380"
  - "redis://192.168.1.156:6379"
  - "redis://192.168.1.156:6380"
  scanInterval: 1000
threads: 0
nettyThreads: 0
codec: !<org.redisson.codec.JsonJacksonCodec> {}
transportMode: "NIO"
```
使用JAVA创建Bean对象

``` java
@Configuration
public class RedissonSpringDataConfig {

	@Bean(name = "redissonConnectionFactory")
	public RedissonConnectionFactory redissonConnectionFactory(RedissonClient redisson) {
		return new RedissonConnectionFactory(redisson);
	}

	@Bean(destroyMethod = "shutdown", name = "redissonClient")
	public RedissonClient redisson(@Value("classpath:/redisson.yaml") Resource configFile) throws IOException {
		Config config = Config.fromYAML(configFile.getInputStream());
		return Redisson.create(config);
	}
}
```

3. 添加启动类型

``` java
@EnableEurekaClient
@SpringBootApplication
public class RedisApplication {
	public static void main(String[] args) {
		SpringApplication.run(RedisApplication.class, args);
	}
}
```
4. 启动项目，查看启动的结果

![启动成功并连接redis建立连接](http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/2019/1/2019-01-02_123941.png)

5. 使用方法

``` java
RedissonConnection connection = new RedissonConnection(redissonClient);
connection.zAdd("abc".getBytes(),123,"1".getBytes());
connection.zAdd("abc".getBytes(),124,"2".getBytes());
connection.zAdd("abc".getBytes(),125,"3".getBytes());
connection.zAdd("abc".getBytes(),126,"4".getBytes());
connection.zAdd("abc".getBytes(),127,"5".getBytes());
```