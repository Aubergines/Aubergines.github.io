---
title: Spring  Cloud注解的使用
tags: spring cloud,注解
date: 2019-1-29 16:47:4
categories: spring cloud
grammar_cjkRuby: true
---

######  前言：编程规范的重要性不言而喻，之前我们接口的传参都是使用Map的形式，随着公司的发展，以及慢慢的规范的建立，我们要求传参的时候尽量使用对象作为入参。Spring boot强制返回的的数据是JSON形式的

1. RequestParam与RequestBody：RequestParam注解接收的参数是来自于request header中，即请求头，也就是在url中，格式为xxx?username=123&password=456，而RequestBody注解接收的参数则是来自于requestBody中，即请求体中。包含RequestBody的接口，前端传递的request header Content-Type必须是JSON串。
如果接口是如下的形式
``` java
@RequestMapping(value = "/getMemberList")
public BaseResponseObject getMemberList(@RequestBody SearchMember searchMember) {
	BaseResponseObject baseResponseObject = new BaseResponseObject(Boolean.FALSE, "", "");
}
```
前端调用的时候只能使用POST的请求方式

``` json
{"visitSiteId":25,"firstResult":0,"maxResult":20,"sortType":2,"memberName":"pppp ppp"}
```
因为这是一个获取列表的请求，一般我们使用的是GET请求，但是GET请求无法传递JSON对象，只能传递键值对的JSON字符串，为了兼容之前的，我只能做个修改
``` java
@RequestMapping(value = "/getMemberList")
public BaseResponseObject getMemberList(SearchMember searchMember) {
	BaseResponseObject baseResponseObject = new BaseResponseObject(Boolean.FALSE, "", "");
}
```
这样前端调用的时候可以使用`/getMemberList?visitSiteId=24&firstResult=0&maxResult=20&memberName=pppp ppp`

2. 关于RequestMapping、PostMapping、GetMapping，后续两个是第一个简写的形式,平时用RequestMapping喜欢`@RequestMapping(value = "/getNodeMapList", method = RequestMethod.GET,produces = MediaType.APPLICATION_JSON)`,如果使用GetMapping就可以像这样`@GetMapping(value = "/getNodeMapList",produces = MediaType.APPLICATION_JSON )`
``` java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@Documented
@RequestMapping(method = RequestMethod.GET)
public @interface GetMapping {

	@AliasFor(annotation = RequestMapping.class)
	String name() default "";
	@AliasFor(annotation = RequestMapping.class)
	String[] value() default {};
	@AliasFor(annotation = RequestMapping.class)
	String[] path() default {};
	@AliasFor(annotation = RequestMapping.class)
	String[] params() default {};
	@AliasFor(annotation = RequestMapping.class)
	String[] headers() default {};
	@AliasFor(annotation = RequestMapping.class)
	String[] consumes() default {};
	@AliasFor(annotation = RequestMapping.class)
	String[] produces() default {};

}

```
PS:注意到我在RequestMapping和GetMapping都设定了`produces`的值，这里的目的是为了把返回值的类型定义为JSON，就是在response header中加上`Content-Type: application/json;charset=UTF-8`,如果没有这个值的话，默认是`Content-Type: application/xhtml+xml;charset=UTF-8`