---
title: Jackson-@JsonInclude之NON_EMPTY 
tags: Jackson,Java,JsonInclude,NON_EMPTY 
date: 2020-11-4 0:3:37
categories: Jackson
grammar_cjkRuby: true
---

@JsonInclude(NON_EMPTY)可用于排除空值。以下值被认为是空的：

 - JsonInclude.Include NON_NULL。
 - 由JsonInclude定义的'Absent'值.Include NON_ABSENT（教程）。
 - 对于Collections和Maps，如果methodisEmpty()返回true。
 - 对于Java数组，如果length = 0。
 - 对于String，如果Strings.length()返回0。

##### 例子

##### Java对象

``` java
@JsonInclude(JsonInclude.Include.NON_EMPTY)
public class Employee {
 	private String name;
 	private String dept;
 	private String address;
 	private List<String> phones;
 	private AtomicReference<BigDecimal> salary;
}
```

###### Main类

``` java
public class ExampleMain {
    public static void main(String[] args) throws IOException {
        Employee employee = new Employee();
        employee.setName("Trish");
        employee.setDept("");
        employee.setAddress(null);
        employee.setPhones(new ArrayList<>());
        employee.setSalary(new AtomicReference<>());

        ObjectMapper om = new ObjectMapper();
        String jsonString = om.writeValueAsString(employee);
        System.out.println(jsonString);
    }
}
```
###### 结果集 
``` json
{"name":"Trish"}
```

##### 不包含NON_EMPTY
如果我们根本不使用@JsonInclude注释，那么上面示例的输出将是：
``` json
{“ name”：“ Trish”，“ dept”：“”，“ address”：null，“ phones”：[]，“ salary”：null}
```
如果我们在Employee类上使用@JsonInclude（JsonInclude.Include.NON_NULL），则输出为：

``` json
{“ name”：“ Trish”，“ dept”：“”，“ phones”：[]，“ salary”：null}
```
如果我们使用@JsonInclude（JsonInclude.Include.NON_ABSENT），则输出将为：
``` json
{“ name”：“ Trish”，“ dept”：“”，“ phones”：[]}
```

