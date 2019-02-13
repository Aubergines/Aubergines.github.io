---
title: 使用thumbnailator处理图片
tags: 图片,裁切,Thumbnails,thumbnailator
date: 2019-2-13 15:21:48
categories: 图片
grammar_cjkRuby: true
---

 1. 按比例进行缩放保存到文件中，其中`scale`是浮点类型的参数，值不能小于0，否则会报[IllegalArgumentException](https://docs.oracle.com/javase/1.5.0/docs/api/java/lang/IllegalArgumentException.html)异常.包含两种用法，一个参数的时候是根据原图片进行等比例的缩放，两个参数的时候第一个参数宽的，第二个是高的缩放比例

```java
Thumbnails.of("c:\\a.jpg").scale(0.3f).toFile("c:\\a1.jpg");
Thumbnails.of("c:\\a.jpg").scale(3f,4f).toFile("c:\\a1.jpg");
```
2. 按照原图等比例缩放到固定的尺寸，以最大的边为限。例如一个`722*450`的图片，等比例缩放到`500*1000`最终图片的尺寸会是`500*312`,如果缩放到`1000*1000`图片的尺寸会是`1000*623`

```java
Thumbnails.of("c:\\a.jpg").size(500,1000).toFile("c:\\a1.jpg");
```
3. 按照给定的尺寸强制缩放到对应的尺寸，如果给定的图片的尺寸和原图的尺寸不一致，处理后的图片会变形

```java
Thumbnails.of("c:\\a.jpg").forceSize(500,1000).toFile("c:\\a1.jpg");
```

4. 指定位置对图片进行处理图片,并指定裁切后的图片的缩放尺寸或大小.其中`sourceRegion`的第一个参数是指定裁切的位置，有`TOP_LEFT`,`TOP_CENTER`,`TOP_RIGHT`,`CENTER_LEFT`等几种情况，后面两个参数是要指定获取的图片的宽高，范围最大为原图的范围，超出范围获取的是原图

```java
Thumbnails.of("c:\\a.jpg").sourceRegion(Positions.CENTER, 300, 300).scale(1).toFile("c:\\a1.jpg");
Thumbnails.of("c:\\a.jpg").sourceRegion(Positions.CENTER, 300, 300)..size(300,400).toFile("c:\\a1.jpg");
```