---
title: hexo + github 搭建博客
author: 方玲涛
top: false
cover: false
toc: true
mathjax: false
date: 2020-08-03 10:51:41
img:
coverImg:
password:
summary:
tags:
- hexo
- github
categories:
- 博客
---
# 前言 

受够了第在第三方网站时充斥着各种广告，偶然间了解到了 `Hexo` 这个静态博客网站，很适合用来写作, 记录笔记,最重要的是它是免费的，里面有许多的博客主题模板，这些主题都是一些很牛的大佬们开发的，而且设计的主题都很棒，看着让我很喜欢，于是开始整理搭建自己的博客。参考了一些大神博客的源码, 中间经历了许多的坎坎坷坷，终于，将博客修改成我想要的样子，现在将博客搭建流程简单记录下分享出来，希望能帮到需要的朋友。



## hexo 简介

1、Hexo 是基于 nodejs 的框架, 是一个快速、简洁且高效的博客框架。

2、[官方文档及官网入口](https://hexo.io)

4、可以配置在很多地方：GitHub、Gitee、Coding 等

## 下载 Git 和 Nodejs

### 安装 Nodejs 

1、首先去官网下载 [Nodejs](https://nodejs.org/en/download/) 推荐LTS (说明：LTS 为长期支持版，Current 为当前最新版)

2、打开下载好的 Nodejs 安装文件，一路点击　Next 

3、 验证安装，并测试是否加入环境变量，`WIN + R` 输入 `cmd` 进入命令行，输入命令 `node -v` 出现 `node` 版本，输入 `npm -v` 出现 `npm` 版本号，表示安装成功，如果报错，则需要将 `Nodejs` 的安装路径加入环境变量。

4、设置 `npm` 的镜像源

```bash
# 查看 npm 的配置
npm config list
# 默认源
npm config set registry https://registry.npmjs.org
# 临时改变镜像源
npm --registry=https://registry.npm.taobao.org
# 永久设置为淘宝镜像源
npm config set registry https://registry.npm.taobao.org
# 另一种方式，编辑 ~/.npmrc 加入下面内容
registry = https://registry.npm.taobao.org
```

5 、设置 npm 的内置路径，全局模块路径和缓存路径，如果你的C盘空间足够大，这一步可以略过，不改变的话，默认路径在：
- npm 包全局目录：C:/Users/[username]/AppData/Roaming/npm/node_modules
- npm 包全局命令目录：C:/Users/[username]/AppData/Roaming/npm
- npm 实际去找全局命令的目录：C:/Users/[username]/.npmrc 文件内容的 prefix 值
- npm 包全局 cache 目录：C:/Users/[username]/.npmrc 文件内容的 cache 值

在你 Node.js 的安装位置，新建两个文件夹，node_global 和 node_cache，我的路径是：

`
D:\Program Files\nodejs\node_global
D:\Program Files\nodejs\node_cache
`
然后分别执行命令：
```bash
npm config set prefix"D:\Program Files\nodejs\node_global"
npm config set cache "D:\Program Files\nodejs\node_cache"
```
### 安装 Git

1、[下载 Git ](https://git-scm.com/)

2、安装 Git 一路 next

3、因为 Git 是分布式版本控制系统，所以需要填写用户名和邮箱作为一个标识，用户和邮箱为你的GitHub 注册的账号和邮箱，鼠标右键打开 `git-bash.exe`
```bash
git config --global user.name "你的 GitHub 用户名"
git config --global user.email "你注册 GitHub 的邮箱"
```
4、为 Github 账户设置 SSH key ( ssh key 是加密传输)首先检查是否生成了秘钥,如果没有生成，那么通过如下命令：
`ssh-keygen -t rsa -C “你的 github 注册邮箱”` 连敲几下回车确认
5、打开你的电脑 C:\Users\Administrator.ssh 里面找到，生成成功后，可以查看 id_rsa.pub 公钥文件

6、为 GitHub 账号配置 `ssh_key：setting—>SSH and GPG keys`
7、验证是否成功输入命令：`ssh -T git@github.com`
正常情况下会出现(有警告可以不用管)：
`Hi Your Name! You've successfully authenticated, but GitHub does not provide shell access.`

## 本地安装 hexo 静态以及发布到 GitHub Pages

### hexo 基本环境安装

1、点击鼠标右键，选择 `Git Bash Here`，然后依次输入如下命令：
`npm install -g hexo-cli`

2、初始化 hexo 程序任意你想要保存博客文件的位置(推荐桌面,文件名随意)输入命令：
`hexo init blog` 可以看到初始化后你的博客目录如下:

```bash
- node_modules //依赖包
- public //博客生成的静态 HTML 文件
- scaffolds //生成文章的模板
- source　// 博客源文件，主要是一些 `md` 文件
    - _data
    - _posts
    - about
    - archives
    - categories
    - friends
    - tags
- themes　// 主题
```

3、hexo 的基本配置，在文件根目录下的 `_config.yml`，就是整个 `hexo` 框架的配置文件了。可以在里面修改大部分的配置。详细可参考官方的 [配置描述](https://hexo.io/zh-cn/docs/configuration)。

### layout （布局）

1、post 

当你每一次使用代码
```bash
hexo new XXX
```
它其实默认使用的是 `post` 这个布局，也就是在 `source` 文件夹下的 `_post` 里面。`Hexo`有三种默认布局：`post`、`page` 和 `draft`，它们分别对应不同的路径，而您自定义的其它布局和 `post` 相同，都将储存到 `source/_posts` 文件夹。而 `new` 这个命令其实是：
```
hexo new [layout] <title>
```
只不过这个 `layout` 默认是 `post` 罢了。

2、page
如果你想另起一页，那么可以使用
```bash
hexo new page newpage
```
系统会自动给你在 `source` 文件夹下创建一个 `newpage` 文件夹，以及 `newpage` 文件夹中的 `index.md`，这样你访问的 `newpage` 对应的链接就是 <http://xxx.xxx/newpage>

3、draft
`draft` 是草稿的意思，也就是你如果想写文章，又不希望被看到，那么可以
```bash
hexo new draft newdraft
```
这样会在 `source/_draft` 中新建一个 `newdraft.md` 文件，如果你的草稿文件写的过程中，想要预览一下，那么可以使用
```bash
hexo server --draft
```
在本地端口中开启服务预览。
如果你的草稿文件写完了，想要发表到 `post` 中，
```bash
hexo publish draft newdraft
```

### 备份博客源文件

有时候我们想换一台电脑继续写博客，最简单的方法就是把博客整个目录拷贝过去，就是这么暴力。不过，这种方法有个问题就是要是那天电脑崩了，本地源文件丢失了，比较麻烦，所以这时候就可以将博客目录下的所有源文件都上传到 `github` 上面。首先在 `github` 博客仓库下新建一个分支 `hexo`，然后 `git clone` 到本地，把 `.git` 文件夹拿出来，放在博客根目录下。
然后 `git branch -b hexo` 切换到 `hexo` 分支，然后 `git add .` ，然后 `git commit -m "xxx"`，最后 `git push origin hexo` 提交就行了。



**[参考文章博客和代码修改搭建](https://shw2018.github.io/posts/6e8839eb.html#toc-heading-16)**
