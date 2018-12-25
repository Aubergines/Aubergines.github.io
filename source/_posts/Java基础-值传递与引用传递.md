---
title: Java基础-值传递与引用传递
tags: Java,值传递,引用传递
date: 2018-12-25 10:50:40
categories: Java基础
grammar_cjkRuby: true
---

##### 前言：今天帮同事处理了一个问题，这是一个关于值传递和引用传递的问题，自己刚好回顾记录一下

1. 整体的逻辑大概类似于如下的代码
``` java
public void test() {
	Map<String,String> tempMap = new HashMap<>(8);
	List<Map> resultList = new ArrayList<>();
	for (int i = 0; i < 2; i++) {
		tempMap.put("key1" + i,"value");
		resultList.add(tempMap);
		System.out.println(resultList);
	}
	System.out.println(resultList);
}
```
输出的结果如下

``` java
[{key10=value}]
[{key11=value, key10=value}, {key11=value, key10=value}]
[{key11=value, key10=value}, {key11=value, key10=value}]
```
看到这个结果，我相信你的第一个感觉是`嗯？`，然后很快就是`嗯！`了，这是一个典型的Java引用传递的问题
2. 关于值传递和引用传递的例子

``` java
public class Deal {

    public static void main(String[] args) {
        int a = 10000;
        addOne(a);
        addTwo(a);
        String s = "abc";
        addOneString(s);
        addTwoString(s);
        StringBuffer sb = new StringBuffer("abc");
        addOneStringBuffer(sb);
        addTwoStringBuffer(sb);
    }

    public static void addOne(int a) {
        System.out.println("执行前addOne后a的值为：" + a);
        a += 1;
        System.out.println("执行后addOne后a的值为：" + a);
    }

    public static void addTwo(int a) {
        System.out.println("执行前addTwo后a的值为：" + a);
        a += 1;
        System.out.println("执行后addTwo后a的值为：" + a);
    }

    public static void addOneString(String s) {
        s += "d";
        System.out.println("执行完addOneString后a的值为：" + s);
    }

    public static void addTwoString(String s) {
        s += "e";
        System.out.println("执行完addTwoString后a的值为：" + s);
    }

    public static void addOneStringBuffer(StringBuffer a) {
        a.append("d");
        System.out.println("执行完addOneStringBuffer后a的值为：" + a);
    }

    public static void addTwoStringBuffer(StringBuffer a) {
        a.append("e");
        System.out.println("执行完addTwoStringBuffer后a的值为：" + a);
    }
}
```
运行结果

``` java
执行前addOne后a的值为：10000
执行后addOne后a的值为：10001
执行前addTwo后a的值为：10000
执行后addTwo后a的值为：10001
执行完addOneString后a的值为：abcd
执行完addTwoString后a的值为：abce
执行完addOneStringBuffer后a的值为：abcd
执行完addTwoStringBuffer后a的值为：abcde
```
分析`addTwo`和`addTwoString`调用前输出，可以知道对于基本类型`int`和字符串类型`String`来说，传递给方法`addTwo`或`addTwoString`的参数a是把a的值传递过去，所以在计算结果的时候，数据没有出现累加的情况，对于`addTwoStringBuffer`可以看出在调用`addTwoStringBuffer`的时候传递过去的值的值加上了之前的结果
3. 问题：`String`和`StringBuffer`都属于字符串类型，为什么一个是传递一个是引用传递
这里我把`s += "d"`换为`s = s.concat("d")`,观察执行结果，发现是一样的，查看`concat`的源码

``` java
public String concat(String str) {
        int otherLen = str.length();
        if (otherLen == 0) {
            return this;
        }
        int len = value.length;
        char buf[] = Arrays.copyOf(value, len + otherLen);
        str.getChars(buf, len);
        return new String(buf, true);
}
```
对比`append`的源码
``` java
public synchronized StringBuffer append(String str) {
	toStringCache = null;
	super.append(str);
	return this;
}
```
可以看到`concat`返回的是一个new出来的新的对象，而`append`返回的确是当前的对象，这就可以得出一个结论，每次对`String`进行操作的时候结果都是一个新的对象，对`StringBuffer`进行类似`append`,`delete`,`deleteCharAt`,`replace`....的操作结果都是当前对象

PS: 平时同事碰到问题喜欢找我来解决，一般我的时间有限，我不会去整体了解问题，我只是抓住他们认为非常诡异的点，想想会有什么问题，提出自己的方案。就像这个问题，前后的逻辑大概有500行的代码，我过去后同事拿出要把整个逻辑都给我讲一件的架势，我立刻说先给我看看结果，对着结果给我说一说有什么问题，抓住关键点我一步一步的问了问题，前后也就五分钟就找到问题点，最开始真没有想到是这么基本的问题。



