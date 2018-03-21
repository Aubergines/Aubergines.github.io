---
title: Spring security安全登录-当AJAX遇上Redirect
tags: spring security、ajax、redirect
date: 2018-3-10 18:11:10
grammar_cjkRuby: true
---
标签： [spring security、ajax、redirect]

前言：最近做平台引入spring security做为安全认证框架，在登录的时候使用的ajax的请求方式，最开始的做法是调用登录的接口，成功后再前端使用`window.location.href = ./index.html`的方式跳转到希望的页面，考虑到以后会根据用户的权限做页面的跳转，就打算在后台做成功页面的跳转，这个时候想到了`Redirect`,当然直接用的话是无法达到跳转的目的的，浏览器只会发生一个对应页面的请求，不会刷新页面
* response.sendRedirect的机制
	* 首先我们要明白用response.sendRedirect做转向的原理，它其实是向浏览器发送一个特殊的Header，然后由浏览器来做转向，转到指定的页面，所以用sendRedirect时，浏览器的地址栏上可以看到地址的变化。
用<jsp:forward page=""/>则不同，它是直接在server做的，浏览器并不知道，也不和浏览器打交道，这从浏览器的地址并不变化可以看出。
所以使用response.sendRedirect时就需要注意以下两点：
1、在使用response.sendRedirect时，前面不能有HTML输出。
这并不是绝对的，不能有HTML输出其实是指不能有HTML被送到了浏览器。事实上现在的server都有cache机制，一般在8K（我是说JSP　SERVER），这就意味着，除非你关闭了cache，或者你使用了out.flush()强制刷新，那么在使用sendRedirect之前，有少量的HTML输出也是允许的。
如果报错说，“一些信息已经被submitted”（原文忘了），那么，你就要注意看了，前面是不是有过多的HTML输出了。
2、在response.sendRedirect之后，应该紧跟一句return;
我们已经知道response.sendRedirect是通过浏览器来做转向的，所以只有在页面处理完成后，才会有实际的动作。既然你已经要做转向了，那么后的输出还有什么意义呢？而且有可能会因为后面的输出导致转向失败。
* ajax请求
	*  Ajax的原理简单来说，实际上就是通过XmlHttpRequest对象来向服务器发异步请求，从服务器获得数据，然后用javascript来操作DOM而更新页面。这其中最关键的一步就是从服务器获得请求数据。要清楚这个过程和原理，我们必须对 XMLHttpRequest有所了解。 
    我们可以看出，XMLHttpRequest对象完全用来向服务器发出一个请求的，它的作用也局限于此，但它的作用是整个ajax实现的关键，我们可以把服务器端看成一个数据接口，它返回的是一个纯文本流，当然，这个文本流可以是XML格式，可以是Html，可以是Javascript代码，也可以只是一个字符串。这时候，XMLHttpRequest向服务器端请求这个页面，服务器端将文本的结果写入页面，这和普通的web开发流程是一样的，不同的是，客户端在异步获取这个结果后，不是直接显示在页面，而是先由javascript来处理，然后再显示在页面。
	
这样看来Ajax 直接对服务端的Response.Redirect是不感冒的, 另觅途径, 具体可行办法如下：
服务端处理如下：
```java
	response.setStatus(200);
	response.getWriter().write("{\"code\":1,\"message\":\"登录成功!\"}");
	response.setContentType("application/json; charset=UTF-8");
	response.setHeader("Redirect", "true");
	response.setHeader("RedirectUrl", "../static/index.html");
	response.flushBuffer();
```
客户端：
```javascript
	$.ajaxSetup({
		complete: function (XMLHttpRequest, textStatus) {
			var redirect = XMLHttpRequest.getResponseHeader("Redirect");
			if (redirect == "true") {
				window.location.href = XMLHttpRequest.getResponseHeader("RedirectUrl");
			}
		}
	});
```
原理：前端通过解析后端的响应头`Header`中包含的信息，做特殊的处理

引用：
[Ajax 如何执行 Response.Redirect][1]
[解决ajax请求下，后台sendRedirect页面跳转无效][2]


  [1]: https://www.cnblogs.com/yipeng-yu/p/7850389.html
  [2]: http://blog.csdn.net/jazywoo123/article/details/7981791