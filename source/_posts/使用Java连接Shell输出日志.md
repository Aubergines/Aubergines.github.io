---
title: 使用Java连接Shell输出日志 
tags: Java,shell
date: 2019-1-30 17:10:3
categories: shell
grammar_cjkRuby: true
---

##### 前言：自己的搭建了一个平台，为了方便查看日志，使用Java自研了一个查看日志的界面。我的做法是使用Java登录到服务器上，根据前端传入的路径，每次最多获取20条的数据，前端每个2s钟刷新一次接口，第一个会根据文件的当前行读取内容，后续会根据前端传入的行数顺序往后读取文件内容。

如下登录Shell的工具类
```java
public class SessionUtil {

	private static final Logger infoLogger = LoggerFactory.getLogger("infoLogger");
	private Session session;

	@Value("${ssh.host}")
	private String sshHost;
	@Value("${ssh.user}")
	private String sshUser;
	@Value("${ssh.passwd}")
	private String sshPasswd;
	
	@PreDestroy
	public void cleanUp() {
		closeConnection();
	}
	public Session getSession() {
		if (session == null) {
			this.session = JschUtil.getSession(sshHost, 22, sshUser, sshPasswd);
		}
		return this.session;
	}
	private void closeConnection() {
		if (session != null) {
			try {
				this.session.disconnect();
				infoLogger.info("关闭Session连接");
				this.session = null;
			} catch (Exception e) {
				e.printStackTrace();
			}
		}
	}
}
```
如下是我的根据前端传入的文件的目录，获取文件的内容

```java
// 列出当前的文件夹下的所有的文件夹
Session session = sessionUtil.getSession();
try {
	ChannelShell channel = (ChannelShell) session.openChannel("shell");
	channel.connect();
	//从远程端到达的所有数据都能从这个流中读取到
	InputStream inputStream = channel.getInputStream();
	//写入该流的所有数据都将发送到远程端。
	OutputStream outputStream = channel.getOutputStream();
	//使用PrintWriter流的目的就是为了使用println这个方法
	PrintWriter printWriter = new PrintWriter(outputStream);
	StringBuilder pathString = new StringBuilder("");
	if (StringUtils.isNotEmpty(source)) {
		String[] split = source.split("_");
		for (String s : split) {
			pathString.append("/").append(s);
		}
	} else {
		pathString.append("/data/logs/");
	}
	String cmd = "wc -l "+ pathString +" |awk '{print $1}'";
	infoLogger.info("cmd = {}",cmd);
	printWriter.println(cmd);
	printWriter.println("exit");//加上个就是为了，结束本次交互
	printWriter.flush();
	BufferedReader in = new BufferedReader(new InputStreamReader(inputStream));
	String msg = "";
	List<String> stringList = new ArrayList<String>();
	boolean isWrite = false;
	while ((msg = in.readLine()) != null) {
		if (isWrite && StringUtils.isNumeric(msg)) {
			stringList.add(msg);
		}
		if (msg.indexOf("wc -l") > 0) {
			isWrite = true;
		}
	}
	resultFile = stringList;
	in.close();
	channel.disconnect();
} catch (Exception e) {
	errorLogger.info(e.toString());
}
```
效果图
![效果图](http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/2019/2/WechatIMG45.jpeg)