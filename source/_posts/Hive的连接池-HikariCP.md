---
title: Hive的连接池-HikariCP
tags: hive,连接池,HikariCP
date: 2018-11-28 20:11:10
grammar_cjkRuby: true
---

#### 前言：之前分享过一篇[使用druid管理Hive的连接池][1],因为我们的大部分的项目是用的都是druid,所以到Hive这块，第一个想到的也是druid，不过作为一个喜欢不断折腾的小青年，最近又尝试了HikariCP，使用起来比druid要简单多了，而且性能上比druid也要强一些。

 1. 先看一下我的pom文件,因为我们的spring-cloud使用的是Edgware.SR3这个版本，spring默认使用的连接池还不是Hikari,所以这里要单独引入HikariCP，spring-boot 2.0就已经使用HikariCP作为默认的连接池，所以使用2.0版本额小伙伴就不用这么麻烦了
	``` xml
	<dependency>
		<groupId>org.springframework.boot</groupId>
		<artifactId>spring-boot-starter-jdbc</artifactId>
		<exclusions>
			<exclusion>
				<groupId>org.apache.tomcat</groupId>
				<artifactId>tomcat-jdbc</artifactId>
			</exclusion>
		</exclusions>
	</dependency>
	<dependency>
		<groupId>com.zaxxer</groupId>
		<artifactId>HikariCP</artifactId>
		<version>2.7.4</version>
	</dependency>
	```
2. 配置中心的配置
	``` yml
	spring:
	  hive:
		minimum-idle: 0
		maximum-pool-size: 2
		max-lifetime: 600000
		connection-timeout: 60000
		username: root
		password: 123456
		jdbc-url: jdbc:hive2://hadoop03:10001/default;transportMode=http;httpPath=cliservice
		driver-class-name: org.apache.hive.jdbc.HiveDriver
		testOnBorrow: false
	```
3. 关于`DataSource`与`JdbcTemplate`的配置文件
	``` java
	@Configuration
	public class JdbcConfiguration {

		@Bean(name = "hiveJdbcDataSource")
		@Primary
		@ConfigurationProperties(prefix = "spring.hive")
		public DataSource hiveDataSource(){
			return DataSourceBuilder.create().build();
		}

		@Bean(name = "hiveJdbcTemplate")
		public JdbcTemplate hiveJdbcTemplate(@Qualifier("hiveJdbcDataSource") DataSource dataSource){
			return new JdbcTemplate(dataSource);
		}
	}
	```
4. 最后一步，愉快的使用，就是这么的简单
	``` java
	@Component
	public class CommonHiveDao {
		private final Logger logger = LoggerFactory.getLogger(this.getClass());
		@Autowired
		@Qualifier("hiveJdbcTemplate")
		JdbcTemplate hiveJdbcTemplate;

		public HiveResponse execute(HiveBean bean){
			hiveJdbcTemplate.execute(bean.getHql());
			HiveResponse hiveResponse = HiveResponse.successResult();
			return hiveResponse;
		}

		public HiveResponse executeQuery(HiveBean bean){
			List<Map<String, Object>> result = hiveJdbcTemplate.queryForList(bean.getHql());
			return HiveResponse.successResult().setListResult(result);
		}
	}
	```

  [1]: https://blog.csdn.net/pa_java/article/details/79638240