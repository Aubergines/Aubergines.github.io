---
title: JSON解析性能对比
tags: JSON,Gson,Fastjson
date: 2018-8-15 20:11:10
grammar_cjkRuby: true
---
前言：我们的项目主要是使用JSON串传递参数，前端调用接口的时候会传递一个JSON串，接口把JSON转成Map,便于编写业务的时候获取数据，今天发现我们把JSON转成Map的使用后了两种方法，突然想看看这两种方法的效率如何，在加上之前我个人非常喜欢的Fastjson

* 测试代码
``` java
String jsonString = "{\"operationIdList\":\"1495435403079\",\"maxResult\":9223372036854775807,\"firstResult\":0}";
for (int j = 0; j < 100; j++) {
	long strartTime = System.currentTimeMillis();
	for (int i = 0; i < 100000; i++) {
		GsonBuilder gb = new GsonBuilder();
		Gson g = gb.create();
		Map<String, String> map = g.fromJson(jsonString,new TypeToken<Map<String, String>>() {}.getType());
		map.get("operationIdList");
	}
	long endTime1 = System.currentTimeMillis();
	System.out.print(endTime1 - strartTime);
	for (int i = 0; i < 100000; i++) {
		Map<String, String> map = ConvertUtil.objToHashMap(jsonString);
		map.get("operationIdList");
	}
	long endTime2 = System.currentTimeMillis();
	System.out.print("===" + (endTime2 - endTime1));
	for (int i = 0; i < 100000; i++) {
		Map<String, String> map = JSON.parseObject(jsonString,Map.class);
		map.get("operationIdList");
	}
	long endTime3 = System.currentTimeMillis();
	System.out.println("===" + (endTime3 - endTime2));
}
```
其中`ConvertUtil.objToHashMap`的具体处理过程如下：
```java
public static HashMap objToHashMap(Object object) {
	HashMap<String, Object> data = new HashMap<String, Object>();
	try {
		JSONObject jsonObject = toJSONObject(object);
		Iterator it = jsonObject.keys();
		while (it.hasNext()) {
			String key = String.valueOf(it.next());
			Object value = jsonObject.get(key);
			data.put(key, value);
		}
	} catch (Exception ex) {
		ex.printStackTrace();
	}
	return data;
}
```
* 测试结果
	* Gson平均用时：632ms
	* JSON平均用时：520ms
	* Fastjson平均用时：73ms
