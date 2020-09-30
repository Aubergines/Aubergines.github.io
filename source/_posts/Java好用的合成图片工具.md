---
title: Java好用的合成图片工具
tags: Java,图片,合成
date: 2020-10-1 0:4:22
categories: Java
grammar_cjkRuby: true
---

##### 前言：互联网本质还是流量的生意，谁收割的流量最多，谁便是下一个赢家。“自古流量出社交”也就可以解释为什么每个APP都有一个社交梦，每个PM都有一颗想做社交的心。无论是B端产品还是C端产品，能够讲好一个“社交故事”，就很有可能给资本市场带来无穷的想象空间。公司一直有分享合成图片的场景，之前自己写起来比较费劲，最近找了一个比较好用的工具分享给大家[码云地址](https://gitee.com/opensourcechen/image-combiner)，感谢opensourcechen的分享。

话不多说，直接上例子看效果
> public void testCombineTopicImage() throws Exception {
        String qrCodeUrl = "http://imgtest.thebeastshop.com/file/combine_image/qrcodef3d132b46b474fe7a9cc6e76a511dfd5.jpg";
        String topicTitle = "# 最爱的家居";
        String topicContent = "井柏然说：“如果没有那个桌子，可能就没有那个水壶”，但是没有桌子，水壶还是那个水壶，只不过是个没有桌子的水壶";
        String topicImage = "https://beast.oss-cn-hangzhou.aliyuncs.com/combine_image/funny_topic/resource/product_3x4.png";
        //背景图
        String bgImage = "https://img.thebeastshop.com/combine_image/funny_topic/resource/bg_3x4.png";
        ImageCombiner imageCombiner = new ImageCombiner(bgImage, OutputFormat.JPG);
        //话题图
        imageCombiner.addImageElement(topicImage, 0, 160, 837, 0, ZoomMode.Width)
                .setRoundCorner(500).setCenter(true);
        //话题标题
        imageCombiner.addTextElement(topicTitle, 55, 150, 1390);
        //话题内容
        imageCombiner.addTextElement(topicContent, 40, 150, 1460)
                .setAutoBreakLine(837, 3, 60);
        //水印
        String waterMark = "https://img.thebeastshop.com/combine_image/funny_topic/resource/water_mark.png";
        imageCombiner.addImageElement(waterMark, 630, 1200);
        //二维码
        imageCombiner.addImageElement(qrCodeUrl, 138, 1707, 186, 186, ZoomMode.WidthHeight);
        //合成图片
        imageCombiner.combine();
        //保存
        imageCombiner.save("/data/logs/aaaaaaaaa1.jpg");
    }
效果图：
![效果图](http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/2020/10/aaaaaaaaa1.jpg)
具体`ImageElement`和`TextElement`对象支持的特性如下表：

| 元素类型        | 特性    | 相关方法                                 |
| ---------      | ---------------------- | ----------------------------------------- |
| `ImageElement` | 图片     | `setImage()`,`setImgUrl()`              |
| `ImageElement` | 位置     | `setX()`,`setY()`                       |
| `ImageElement` | 缩放     | `setWidth()`,`setHeight()`,`ZoomMode`   |
| `ImageElement` | 圆角     | `setRoundCorner()`                      |
| `ImageElement` | 居中绘制 | `setCenter()`                           |
| `ImageElement` | 透明度   | `setAlpha()`                            |
| ----------------- |  |  |
| `TextElement`  | 文本     | `setText()`,`setY()`                    |
| `TextElement`  | 位置     | `setX()`,`setY()`                       |
| `TextElement`  | 居中绘制 | `setCenter()`                           |
| `TextElement`  | 透明度   | `setAlpha()`                            |
| `TextElement`  | 颜色     | `setColor()`                            |
| `TextElement`  | 字体     | `setFontName()`                         |
| `TextElement`  | 字号     | `setFontName()`                         |
| `TextElement`  | 删除线   | `setStrikeThrough()`                    |
| `TextElement`  | 自动换行 | `setAutoBreakLine()`                    |
