---
title: mysql 日期 字符串 时间戳互传
author: 方玲涛
top: false
cover: true
toc: true
mathjax: false
date: 2020-12-15 22:07:06
img:
coverImg:
password:
summary:
tags:
- mysql
categories:
- mysql
---


# mysql 日期 字符串 时间戳互传

平时比较常用的时间、字符串、时间戳之间的互相转换，虽然常用但是几乎每次使用时候都喜欢去搜索一下用法；本文将作为一个笔记，整理一下三者之间的 转换（即：date转字符串、date转时间戳、字符串转date、字符串转时间戳、时间戳转date，时间戳转字符串）用法，方便日后查看；

### 涉及的函数

```sql
date_format(date, format) 函数，MySQL日期格式化函数date_format()

unix_timestamp() 函数

str_to_date(str, format) 函数

from_unixtime(unix_timestamp, format) 函数，MySQL时间戳格式化函数from_unixtime
```

### 时间转字符串

`
select date_format(now(), '%Y-%m-%d');

结果：2020-12-15
`
### 时间转时间戳

`select unix_timestamp(now());`

`//结果：1608041813`

### 字符串转时间戳

`select unix_timestamp('2020-12-15');
 
 `//结果：1608041813`
 
### 时间戳转时间

`select from_unixtime(1608041813);`

`//结果：2020-12-15 20:45:24`

### 时间戳转字符串

`select from_unixtime(1608041813,'%Y-%d');`

`//结果：2020-12-15 20:45:24`

# 附表

### MySQL日期格式化（format）取值范围。

```sql
值   含义
秒   %S、%s   两位数字形式的秒（ 00,01, ..., 59）
分   %I、%i   两位数字形式的分（ 00,01, ..., 59）
小时  %H  24小时制，两位数形式小时（00,01, ...,23）
%h  12小时制，两位数形式小时（00,01, ...,12）
%k  24小时制，数形式小时（0,1, ...,23）
%l  12小时制，数形式小时（0,1, ...,12）
%T  24小时制，时间形式（HH:mm:ss）
%r   12小时制，时间形式（hh:mm:ss AM 或 PM）
%p  AM上午或PM下午
  周      %W 一周中每一天的名称（Sunday,Monday, ...,Saturday）
 %a 一周中每一天名称的缩写（Sun,Mon, ...,Sat）
%w  以数字形式标识周（0=Sunday,1=Monday, ...,6=Saturday）
%U  数字表示周数，星期天为周中第一天
%u  数字表示周数，星期一为周中第一天
天   %d  两位数字表示月中天数（01,02, ...,31）
%e   数字表示月中天数（1,2, ...,31）
 %D 英文后缀表示月中天数（1st,2nd,3rd ...）
 %j 以三位数字表示年中天数（001,002, ...,366）
月   %M  英文月名（January,February, ...,December）
%b  英文缩写月名（Jan,Feb, ...,Dec）
%m  两位数字表示月份（01,02, ...,12）
%c  数字表示月份（1,2, ...,12）
年   %Y  四位数字表示的年份（2015,2016...）
%y   两位数字表示的年份（15,16...）
文字输出    %文字     直接输出文字内容
```