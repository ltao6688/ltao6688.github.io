---
title: java IO(二) 字节流 OutputStream
author: 方玲涛
top: false
cover: true
toc: true
mathjax: false
date: 2020-08-31 14:58:53
img:
coverImg:
password:
summary:
tags:
- IO流OutputStream
categories:
- java
---

# OutputStream 

OutputStream 继承关系图

![继承关系图](https://raw.githubusercontent.com/ltao6688/blogPic/master/blog/img/20200901215357.png)

OutputStream 实现了 Closeable 和 Flushable 接口，代表着可关闭，可冲刷。

## 方法

```java
public abstract void write(int b)
// 写入一个字节，可以看到这里的参数是一个 int 类型，对应上面的读方法，int 类型的 32 位，只有低 // 8 位才写入，高 24 位将舍弃。

public void write(byte b[])
// 将数组中的所有字节写入，和上面对应的 read() 方法类似，实际调用的也是下面的方法。

public void write(byte b[], int off, int len)
// 将 byte 数组从 off 位置开始，len 长度的字节写入

public void flush()
// 强制刷新，将缓冲中的数据写入

public void close()
// 关闭输出流，流被关闭后就不能再输出数据了

```

从源码角度，与输入流的 read 方法相同，提供只有一个参数的write方法，并将另外两个参数设置默认值为0。在真正被调用的 `write(byte[], int off, int len)` 方法会调用抽象方法`write(byte)`。