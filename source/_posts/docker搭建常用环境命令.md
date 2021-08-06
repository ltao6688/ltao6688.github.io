---
title: docker搭建常用环境命令
author: 方玲涛
top: false
cover: true
toc: true
mathjax: false
date: 2021-08-05 11:32:03
img:
coverImg:
password:
summary:
tags:
categories:
---
# docker的安装

### 安装
```bash 
sudo yum install -y yum-utils

```
### 添加镜像源
```bash
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

### 倒序列出docker版本
```bash
yum list docker-ce --showduplicates | sort -r

# 安装dokcer
sudo yum install docker-ce-18.06.1.ce-3.el7 docker-ce-cli-18.06.1.ce-3.el7 containerd.io
# 启动docker
sudo systemctl start docker-ce
```

### 卸载重装
yum list installed|grep docker


### 搭建redis
* redis-server --appendonly yes : 在容器执行redis-server启动命令，并打开redis持久化配置

* --requirepass "123456"  设置引号里字符为密码

* --restart=always : 容器随docker启动而启动
```bash
docker run  --restart=always --name redis5.0 -v $PWD/redis/data:/data -d -p 6379:6379 redis:5.0.4 redis-server --appendonly yes --requirepass "123456"
```
### 搭建mysql  

* --lower_case_table_names 需放到镜像后面 否则报错
```bash
docker run -d --name mysql57 --restart=always -v $PWD/mysql57:/var/lib/mysql -v $PWD/mysql57-conf:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=root  -p 3306:3306 mysql:5.7 --lower_case_table_names=1
```

### 搭建 jenkins
```bash
docker run -u root --rm -d -p 8080:8080 -p 50000:50000 -v $PWD/jenkins-data:/var/jenkins_home -v /var/run/docker.sock:/var/run/docker.sock jenkinsci/blueocean 
```
```bash
docker run -e MODE=standalone -d --name nacos --restart=always -v $PWD/conf:/home/nacos/conf -v $PWD/logs:/home/nacos/logs -p 8848:8848 nacos/nacos-server:2.0.0
```
```bash
docker run -itd --name mongo -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=root -e MONGO_INITDB_ROOT_PASSWORD=root mongo --auth
```