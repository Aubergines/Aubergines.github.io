---
title: Hive Beeline 的使用
tags: Hive,Beeline,HiveServer2
date: 2018-2-20 20:11:10
grammar_cjkRuby: true
---
前言：官方文档这样来说HiveServer2`HiveServer2 (introduced in Hive 0.11) has its own CLI called Beeline.  HiveCLI is now deprecated in favor of Beeline, as it lacks the multi-user, security, and other capabilities of HiveServer2.`。HiveServer是一种可选服务，允许远程客户端可以使用各种编程语言向Hive提交请求并检索结果。HiveServer是建立在Apache ThriftTM（http://thrift.apache.org/） 之上的，因此有时会被称为Thrift Server，这可能会导致混乱，因为新服务HiveServer2也是建立在Thrift之上的．自从引入HiveServer2后，HiveServer也被称为HiveServer1。HiveServer2(HS2)是一种能使客户端执行Hive查询的服务。 HiveServer2是HiveServer1的改进版，HiveServer1已经被废弃。HiveServer2可以支持多客户端并发和身份认证。旨在为开放API客户端（如JDBC和ODBC）提供更好的支持。HiveServer2单进程运行，提供组合服务，包括基于Thrift的Hive服务（TCP或HTTP）和用于Web UI的Jetty Web服务器

1. HiveServer2 的配置
	1. hive-site.xml 中的配置
	```
	<!-- 开启HiveServer2的认证模式 -->
	<property>
		<name>hive.server2.authentication</name>
		<value>CUSTOM</value>
	</property>
	<!-- 配置自定义验证类，需要自己编写，下面会给我具体的代码 -->
	<property>
		<name>hive.server2.custom.authentication.class</name>
		<value>com.allin.auth.CustomPasswdAuthenticator</value>
	</property>
	<!-- hive-site.xml中配置用户密码 -->
	<property>
		<name>hive.jdbc_passwd.auth.root</name>
		<value>123456</value>
	</property>
	<property>
		<name>hive.metastore.schema.verification</name>
		<value>false</value>
	</property>
		<!--HiveServer2 配置启动端口，默认是10000 -->
	<property>
			<name>hive.server2.thrift.http.port</name>
			<value>10086</value>
	</property>
	<!--HiveServer2 配置连接方式，Expects one of [binary, http].Transport mode of HiveServer2 -->
	<property>
			<name>hive.server2.transport.mode</name>
			<value>http</value>
	</property>
	<property>
		<name>hive.server2.thrift.http.path</name>
		<value>cliservice</value>
		<description>Path component of URL endpoint when in HTTP mode.</description>
	 </property>
	 <property>
			<name>hive.server2.thrift.max.worker.threads</name>
			<value>500</value>
	 </property>
	 <property>
			<name>hive.server2.thrift.min.worker.threads</name>
			<value>5</value>
	 </property>
	 <!--重要配置，默认情况下，HiveServer2以提交查询的用户执行查询。Setting this property to true will have HiveServer2 execute Hive operations as the user making the calls to it -->
	 <property>
			<name>hive.server2.enable.doAs</name>
			<value>false</value>
	 </property>
	```
	2. 自定义验证类，实现`PasswdAuthenticationProvider`即可
	```
	public class CustomPasswdAuthenticator implements PasswdAuthenticationProvider {
		private Logger LOG = LoggerFactory.getLogger(CustomPasswdAuthenticator.class);
		private Configuration conf = null;

		@Override
		public void Authenticate(String userName, String passwd) throws AuthenticationException {
			this.LOG.info("user: " + userName + " try login.");
			String passwdConf = getConf().get(String.format("hive.jdbc_passwd.auth.%s", new Object[]{userName}));

			if (passwdConf == null) {
				String message = "user's ACL configration is not found. user:" + userName;
				this.LOG.info(message);
				throw new AuthenticationException(message);
			}

			if (!passwd.equals(passwdConf)) {
				String message = "user name and password is mismatch. user:" + userName;
				throw new AuthenticationException(message);
			}
		}

		public Configuration getConf() {
			if (this.conf == null) {
				this.conf = new Configuration(new HiveConf());
			}
			return this.conf;
		}

		public void setConf(Configuration conf) {
			this.conf = conf;
		}
	}
	```
2. 命令行中使用HiveServer2
	 1. 启动`hiveserver2`进程：`hive --service hiveserver2 &`
	 2. 进入CLI命令行，即beeline：`beeline`
	 3. 连接hiveserver2服务，对应的用户名和密码是在`hive-site.xml`中配置的认证用的用户名和密码：` !connect jdbc:hive2://127.0.0.1:10001/default;transportMode=http;httpPath=cliservice root` 后面跟着的`root`表示用户名，这里可以不写，回车后会有相应的提示
	 
	 ![进入命令行][1]
	 4. 使用，使用方法和Hive的CLI类似
	
	 ![使用方法][2]

3. 使用JAVA客户端连接HiveServer2
	1. 下面展示一下我写的1.0 版本的连接池，后续使用了`druid`来维护这个连接池
	```
	public class HiveUtils {
		@Value("${hive.jdbc}")
		private  String JDBC = "";
		@Value("${hive.user}")
		private  String USER = "";
		@Value("${hive.password}")
		private  String PASSWORD = "";
		private  Connection con = null;
		public synchronized Connection getConnection() {
			try {
				if (con == null){
					System.out.println(JDBC + "=======" + USER + "==========" + PASSWORD);
					Class.forName("org.apache.hive.jdbc.HiveDriver");
					con = DriverManager.getConnection(JDBC , USER, PASSWORD);
				}
				return con;
			} catch (Exception e) {
				e.printStackTrace();
			}
			return null;
		}
	}
	```
	2. 测试查询,对返回数据做了一下简单的封装
	```
		Connection connection = hiveUtils.getConnection();
		java.sql.Statement statement = connection.createStatement();
		// 获取所有字段
		if (StringUtils.isNotBlank(database) && StringUtils.isNotBlank(tableName)) {
			List<String> columns = getColumns(map);
			// 执行sql查询数据
			ResultSet resultSet1 = statement.executeQuery(sql);
			int size = columns.size();
			List<Map<String, String>> resultList = new ArrayList<>();
			while (resultSet1.next()) {
				Map<String, String> lineMap = new HashMap<>(size);
				for (int i = 0; i < size; i++) {
					lineMap.put(columns.get(i), resultSet1.getString(i + 1));
				}
				resultList.add(lineMap);
			}
			returnMap.put("num", resultList.size());
			returnMap.put("result", resultList);
		}
	```


引用：
[HiveServer2概述][3]


  [1]: http://ov138d8j2.bkt.clouddn.com/2018/3/21/2018-03-21_092547.png "命令行使用HiveServer2"
  [2]: http://ov138d8j2.bkt.clouddn.com/2018/3/21/2018-03-21_101731.png "使用方法"
  [3]: https://www.cnblogs.com/xiaozhang123/p/7310980.html