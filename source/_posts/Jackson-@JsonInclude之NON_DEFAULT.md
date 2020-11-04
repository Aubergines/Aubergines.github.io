---
title: Jackson-@JsonInclude之NON_DEFAULT
tags: Jackson,Java,JsonInclude,NON_DEFAULT
date: 2020-11-4 0:14:8
categories: Jackson
grammar_cjkRuby: true
---

@JsonInclude（JsonInclude.Include.NON_DEFAULT）可用于排除具有POJO默认值的属性。这有不同的用法，如下所示：

 - 如果在类级别使用@JsonInclude（JsonInclude.Include.NON_DEFAULT），则将排除字段的默认值。这是通过使用零参数构造函数创建POJO实例并比较属性值（不包括默认值，例如，默认int值为0，默认String值为null等）来完成的。
 - 如果在属性级别使用@JsonInclude（JsonInclude.Include.NON_DEFAULT）或使用：ObjectMapper.setDefaultPropertyInclusion（JsonInclude.Include.NON_DEFAULT）;在两种情况下：
	 - 所有被视为“空”的值（根据NON_EMPTY） 被排除在外
	 - 原始/包装默认值被排除
	 - 排除毫秒值为“ 0L”的日期/时间值

##### 例子

##### Java对象

##### 在Class上使用NON_DEFAULT
``` java
@JsonInclude(JsonInclude.Include.NON_DEFAULT)
public class Employee {
     private String name;
     private String dept;
     private Integer salary;
     private boolean fullTime;
     private List<String> phones;
     private Date dateOfBirth;
}
```
##### Main类
``` java
public class ExampleMain {
    public static void main(String[] args) throws IOException {
        Employee employee = new Employee();
        employee.setName("Trish");
        employee.setFullTime(false);
        employee.setPhones(new ArrayList<>());
        employee.setSalary(Integer.valueOf(0));
        employee.setDateOfBirth(new Date(0));

        ObjectMapper om = new ObjectMapper();
        String jsonString = om.writeValueAsString(employee);
        System.out.println(jsonString);
    }
}
```

##### 结果
``` json
{"name":"Trish","salary":0,"phones":[],"dateOfBirth":0}
```
从上面的输出中可以看出，只有具有默认成员值的属性被排除。不排除带0的整数（原始包装器 - salary），不排除带0毫秒的日期，也不排除“空”收集（phones）。

###### 在属性上使用NON_DEFAULT
``` java
public class Employee2 {
    @JsonInclude(JsonInclude.Include.NON_DEFAULT)
    private String name;
    @JsonInclude(JsonInclude.Include.NON_DEFAULT)
    private String dept;
    @JsonInclude(JsonInclude.Include.NON_DEFAULT)
    private Integer salary;
    @JsonInclude(JsonInclude.Include.NON_DEFAULT)
    private boolean fullTime;
    @JsonInclude(JsonInclude.Include.NON_DEFAULT)
    private List<String> phones;
    @JsonInclude(JsonInclude.Include.NON_DEFAULT)
    private Date dateOfBirth;
}
```

``` java
public class ExampleMain2 {
    public static void main(String[] args) throws IOException {
        Employee2 employee = new Employee2();
        employee.setName("Trish");
        employee.setFullTime(false);
        employee.setPhones(new ArrayList<>());
        employee.setSalary(Integer.valueOf(0));
        employee.setDateOfBirth(new Date(0));

        ObjectMapper om = new ObjectMapper();
        String jsonString = om.writeValueAsString(employee);
        System.out.println(jsonString);
    }
}
```

##### 结果

``` json
{"name":"Trish"}
```

##### 全局使用NON_DEFAULT

``` java
public class ExampleMain3 {
    public static void main(String[] args) throws IOException {
        Employee2 employee = new Employee2();
        employee.setName("Trish");
        employee.setFullTime(false);
        employee.setPhones(new ArrayList<>());
        employee.setSalary(Integer.valueOf(0));
        employee.setDateOfBirth(new Date(0));

        ObjectMapper om = new ObjectMapper();
        om.setDefaultPropertyInclusion(JsonInclude.Include.NON_DEFAULT);
        String jsonString = om.writeValueAsString(employee);
        System.out.println(jsonString);
    }
}
```

###### 结果

``` json
{"name":"Trish"}
```
如上所示，最后两个选项产生了相同的结果，即“空”值，原始/包装器默认值和具有0L毫秒的日期也被排除在外。

