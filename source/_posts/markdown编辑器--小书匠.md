---
title: markdown编辑器--小书匠
notebook: 生活笔记
date: 2017-8-25 13:24:37
tags: [markdown，小书匠]
---
# 背景
在widow中使用markdown来编写blog，编辑器的选择一直是一个让我相当头疼的问题，我曾经尝试过网页版的[`马克飞象`](https://maxiang.io/)，说实在的确实使用起来挺好的，尤其是我一直使用印象笔记，马克飞象可以很方便的把内容同步到印象笔记中，美中不足的是这是一个付费的软件。因为一直使用[`Sublime Text`](http://www.sublimetext.com/)，相对来说Sublime Text对markdown的支持还是不错的，后来发现其中有一个插件`Evernote`可以实现与印象笔记的同步的效果，就一直使用这个插件编写笔记，这个插件有一个比较不好的地方是对格式的检查是比较严格的，经常在保存的时候报各种错误，让人感觉用起来和难受。最近实在受不了这种降低自己工作效率的工具就在网上搜索了一下，在知乎的一个回答了偶然发现了[`小书匠`](http://soft.xiaoshujiang.com/)
# 优点

 1. 文档的同步：小书匠提供了多种的可以保存在第三方的接口，经过简单的配置我们就可以把自己的写作的文档保存在想要存放的平台上，比如印象笔记、为知、GitHub上，如图所示：
 
  ![enter description here][1]
我自己选择的配置的是把印象笔记作为我存储文档的地方，只要填写一个由印象笔记提供的token就可以，非常的简单，配置完成后再编辑区的左边会有印象笔记的目录，我配置好后的效果：

 ![enter description here][2]
 2. 图片的存储：小书匠提供了七牛云、腾讯云、阿里云等第三方的存储图片的接口，用户根据不同的平台的配置的不同的需求，添加相应的配置。[七牛](https://www.qiniu.com/)需要申请一个账号，在对象存储创建一个存储空间，在个人中心中获取`AccessKey/SecretKey`，添加到小书匠图床服务的七牛云存储对应的配置项里面就可以，另外图片的路径可以加上`year`,`month`,`date`等自定义的配置，我的配置是`/{{year}}/{{month}}/{{date}}/{{filename}}`,这样生成的图片的地址就会是域名加上`/2017/8/24/filename`.

![enter description here][3]
 3. 编辑器的配置：小书匠提供了多种样式配置编辑器，还有多种多样的主题，对于喜欢折腾的人来说还是挺不错的，避免的审美疲劳
 
![enter description here][4]
 4. 问题反馈建议：在[Github](https://github.com/suziwen/markdownxiaoshujiang/issues/)作者提供了一个大家反馈问题的渠道。


  [1]: http://ov138d8j2.bkt.clouddn.com/2017/8/24/2017-08-24_174208.png "第三方存储"
  [2]: http://ov138d8j2.bkt.clouddn.com/2017/8/24/2017-08-24_180445.png "印象笔记目录"
  [3]: http://ov138d8j2.bkt.clouddn.com/2017/8/24/2017-08-24_174220.png "图床服务"
  [4]: http://ov138d8j2.bkt.clouddn.com/2017/8/24/2017-08-24_174330.png "自定义编辑器"