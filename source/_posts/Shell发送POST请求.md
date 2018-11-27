---
title: Shell发送POST请求
tags: shel,post
date: 2018-11-27 20:11:10
grammar_cjkRuby: true
---

#### 有些场景用crontab跑定时任务，今天有一个需求调用的接口，接收的请求形式是POST的，纠结了一段时间，终于搞出来了

> curl -i -X POST -H "'Content-type':'application/json'" -d '{"zuultoken":"abc","currentDate":"2018-11-13 11:46:18"}' http://msa.aubergine.cc/zsq-platfor
m/customer/movement/v3/delateOverdueData