---
title: 使用thumbnailator处理图片
tags: 图片,裁切,Thumbnails,thumbnailator
date: 2019-2-13 15:21:48
categories: 图片
grammar_cjkRuby: true
---

##### 前言：使用JDK自带的方法处理图片非常的不方便，目前处理图片主流的有两种，第一种依赖环境需要安装`imageMagick`和`GraphicsMagick`，第二种是使用Google开源的`Thumbnailator`这个使用起来简单点，不过相对来说`Thumbnailator`在处理图片的时候还原度没有第一种的好，不过能满足绝大多数的情况。

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
5. 对图片进行旋转

```java
Thumbnails.of("c:\\a.jpg").size(500,1000).rotate(90).toFile("c:\\a1.jpg");
```
6.对图片打水印

```java
Thumbnails.of(new File("original.jpg"))
        .size(160, 160)
        .rotate(90)
        .watermark(Positions.BOTTOM_RIGHT, ImageIO.read(new File("watermark.png")), 0.5f)
        .outputQuality(0.8)
        .toFile(new File("image-with-watermark.jpg"));
```