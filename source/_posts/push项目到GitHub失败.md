---
title: push项目到GitHub失败
tags: github,push,项目
date: 2018-12-25 22:50:57
categories: 工作杂记
grammar_cjkRuby: true
---
##### 这两天使用Mac新建立的项目Push到GitHub上得时候报了一个403的异常，如下是我的解决的方法

``` java?linenums
2:39 PM	Can't finish GitHub sharing process
				Successfully created project 'spring-cloud' on GitHub, but initial push failed:
				unable to access 'https://github.com/Aubergines/spring-cloud.git/': The requested URL returned error: 403
2:44 PM	Push failed: Failed with error: unable to access 'https://github.com/Aubergines/spring-cloud.git/': The requested URL returned error: 403
```
打开到项目的文件夹下的`.git/config`文件,`url`中加上`Aubergines@`

``` java?linenums
[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
        ignorecase = true
        precomposeunicode = true
[remote "origin"]
        url = https://Aubergines@github.com/Aubergines/spring-cloud.git
        fetch = +refs/heads/*:refs/remotes/origin/*
[branch "master"]
        remote = origin
        merge = refs/heads/master
```

