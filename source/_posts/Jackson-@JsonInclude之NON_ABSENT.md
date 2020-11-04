---
title: Jackson-@JsonInclude之NON_ABSENT 
tags: Jackson,Java,JsonInclude,NON_ABSENT 
date: 2020-11-3 23:53:52
categories: Jackson
grammar_cjkRuby: true
---

@JsonInclude(NON_ABSENT)可用于排除空值和“不存在”的值。此处的不存在值表示引用空值的非空引用类型值（例如java.utl.concurrent.atomic.AtomicReference）。


#### 例子

##### 对象

``` java
@JsonInclude(JsonInclude.Include.NON_ABSENT)
public class Employee {
 	private String name;
 	private String dept;
 	private AtomicReference<String> address;
}
```

##### Main类

``` java
public class ExampleMain {
    public static void main(String[] args) throws IOException {
        Employee employee = Employee.of("Trish", null, new AtomicReference<>());
        ObjectMapper om = new ObjectMapper();
        String jsonString = om.writeValueAsString(employee);
        System.out.println(jsonString);
    }
}
```

``` json
{"name":"Trish"}
```

###### 不加NON_ABSENT注释
如果我们根本不使用@JsonInclude，那么上面示例的输出将是：
``` json
{“ name”：“ Trish”，“ dept”：null，“ address”：null}
```
如果我们使用@JsonInclude（JsonInclude.Include.NON_NULL），则输出为：
``` json
{“ name”：“ Trish”，“ address”：null}
```

