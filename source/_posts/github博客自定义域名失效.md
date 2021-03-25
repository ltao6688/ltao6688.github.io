---
title: github博客自定义域名失效
author: 方玲涛
top: false
cover: true
toc: true
mathjax: false
date: 2020-12-06 20:44:54
img:
coverImg:
password:
summary:
tags:
- 博客
- hexo
categories:
- 博客
---

# 每次发布文章到博客 配置的域名便会失效

## 解决办法

### 方法一

只要在source 目录添加一个新文件CNAME就好

CNAME –不带任何后缀，这就是全称，里面写的是你的域名

### 方法二

安装插件 `npm install hexo-generator-cname --save`

在博客的配置文件中 `_config.yml` 中添加一条

Plugins:
- hexo-generator-cname

可能有同学跟博主一样，发现第一个解决方法不能用，其实原因很简单

你把解决方法二里面的插件也安装啦，两个有冲突，只需要执行

`npm uninstall hexo-generator-cname --save`

之后你会发现，按照第一个解决方法完全OK！


[转载](https://blog.csdn.net/KK_2018/article/details/90518042)