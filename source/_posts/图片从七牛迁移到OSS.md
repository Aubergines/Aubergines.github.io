---
title: 图片从七牛迁移到OSS 
tags: 图片,七牛,OSS
date: 2018-11-27 20:11:10
grammar_cjkRuby: true
---
#### 前言： 最近有需要把存储在七牛的图片迁移到OSS上，记录一下迁移的过程

 1. 参考阿里云的文档：[点我看详情][1]
 2. 我的配置
	 * 修改srcType为`srcType=qiniu`
	 * 配置上七牛的`srcAccessKey`,`srcSecretKey`,`srcDomain`,`srcBucket`,其中srcDomain是空间配置的域名

	![srcDomain获取位置][2]
	 *  配置`srcPrefix`,看个人的需求，如果是只迁移某一个目录的话，那就什么都不用填写
	 *  配置阿里云OSS需要的`destAccessKey`,`destSecretKey`,`destBucket`
	 *  根据环境启动对应的脚本就可以了
3. 因为这个的迁移是基于上传下载文件的，所以如果需要迁移的内容比较多，要考虑带宽


  [1]: https://yq.aliyun.com/articles/483905
  [2]: http://zsq-blog-image.oss-cn-beijing.aliyuncs.com/2018/11/2018-11-27_173100.png "srcDomain获取位置"