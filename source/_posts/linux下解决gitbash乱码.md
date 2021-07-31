---
title: linux下解决gitbash乱码
author: 方玲涛
top: false
cover: true
toc: true
mathjax: false
date: 2021-07-31 10:39:10
img:
coverImg:
password:
summary:
tags:
- git
- linux
categories:
- linux
---

# linux下解决gitbash乱码

切换到 ubuntu 有一段时间了，最近用 vscode 写代码使用 git commit 时发现文件名和日志乱码。会把中文显示为一串数字。

这个时候只需要添加相应的配置即可显示正常的中文，执行以下命令即可
```bash
git config --global core.quotepath false
```
或者手动修改配置文件 ~/.gitconfig，添加以下内容
```bash
[core]
    quotepath = false
```