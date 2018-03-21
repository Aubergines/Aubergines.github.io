---
title: 使用druid管理Hive的连接池 
tags: hive,druid,连接池,HiveServer2
date: 2018-2-25 10:11:10
grammar_cjkRuby: true
---
前言：HiveServer2可以使用多种语言作为客户端，连接改服务，进行Hive数据的查询与处理，之前使用的是自己的维护的连接池，后来做了一下改进，使用的了阿里的`druid`维护这个连接池，我使用的是`Maven`,下面就介绍一下我的整个项目的配置

1. pom.xml文件的配置
	```
	 <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-jdbc</artifactId>
     </dependency>
	 <dependency>
		<groupId>org.apache.hive</groupId>
		<artifactId>hive-jdbc</artifactId>
		<version>2.1.1</version>
	</dependency>
	<dependency>
		<groupId>com.alibaba</groupId>
		<artifactId>fastjson</artifactId>
		<version>1.2.44</version>
	</dependency>
	<dependency>
		<groupId>com.alibaba</groupId>
		<artifactId>druid</artifactId>
		<version>1.1.5</version>
	</dependency>

	```
2. application.yml中的配置,各个配置的含义，我就不一一说，网上对此有比较详细的描述
	```
	hive:
	  jdbc: jdbc:hive2://127.0.0.1:10086/default;transportMode=http;httpPath=cliservice
	  type: com.alibaba.druid.pool.DruidDataSource
	  driver-class-name: org.apache.hive.jdbc.HiveDriver
	  user: root
	  password: 123456
	  max-active: 5
	  initialSize: 3
	  maxWait: 60000
	  minIdle: 1
	  timeBetweenEvictionRunsMillis: 60000
	  minEvictableIdleTimeMillis: 300000
	  testWhileIdle: true
	  validationQuery: SELECT 1
	  testOnBorrow: false
	  testOnReturn: false
	  poolPreparedStatements: true
	  maxOpenPreparedStatements: 50
	  removeAbandoned: true
	  removeAbandonedTimeout: 180
	```
3. 连接池的配置的
	```
	public class HiveDataSource {
		@Autowired
		private Environment env;
		@Bean(name = "hiveJdbcDataSource")
		@Qualifier("hiveJdbcDataSource")
		public DataSource dataSource() {
			DruidDataSource dataSource = new DruidDataSource();
			dataSource.setUrl(env.getProperty("hive.jdbc"));
			dataSource.setDriverClassName(env.getProperty("hive.driver-class-name"));
			dataSource.setUsername(env.getProperty("hive.user"));
			dataSource.setPassword(env.getProperty("hive.password"));
			dataSource.setTestWhileIdle(Boolean.valueOf(env.getProperty("hive.testWhileIdle")));
			dataSource.setValidationQuery(env.getProperty("hive.validationQuery"));
			dataSource.setMaxActive(Integer.valueOf(env.getProperty("hive.max-active")));
			dataSource.setInitialSize(Integer.valueOf(env.getProperty("hive.initialSize")));
			dataSource.setRemoveAbandoned(Boolean.valueOf(env.getProperty("hive.removeAbandoned")));
			dataSource.setRemoveAbandonedTimeout(Integer.valueOf(env.getProperty("hive.removeAbandonedTimeout")));
			return dataSource;
		}
		@Bean(name = "hiveJdbcTemplate")
		public JdbcTemplate hiveJdbcTemplate(@Qualifier("hiveJdbcDataSource") DataSource dataSource) {
			return new JdbcTemplate(dataSource);
		}
	}
	```
4. 使用方法
	1. 注入Template 
	```
	@Autowired
	@Qualifier("hiveJdbcTemplate")
	JdbcTemplate hiveJdbcTemplate;
	```
	2. 可以尽情的使用spring JDBCTemplate提供的方法查询数据,特别的爽
	```
	List<Map<String, Object>> mapList = hiveJdbcTemplate.queryForList(sql);
	int size = columns.size();
	List<Map<String, String>> resultList = new ArrayList<Map<String, String>>();
	for (Map<String, Object> stringMap : mapList) {
		Map<String, String> lineMap = new HashMap<>(size);
		for (int i = 0; i < size; i++) {
			Object columnValue = stringMap.get(tableName + "." + columns.get(i));
			if (columnValue != null) {
				lineMap.put(columns.get(i), stringMap.get(tableName + "." + columns.get(i)).toString());
			} else {
				lineMap.put(columns.get(i), "");
			}
		}
		resultList.add(lineMap);
	}
	```