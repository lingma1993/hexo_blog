---
title: Python命令行查询成都铁路局12306检票口信息
date: 2017-07-04 21:16:33
tags: python
categories: 开发笔记
copyright: true
---

> Github地址：
>
> [Find-Train-Ticket-Check-in]: https://github.com/lingma1993/Find-Train-Ticket-Check-in

## 背景

近日多地大雨导致铁路多趟列车停运，自己关注成都铁路12306微信公众号，发现一个新的侯乘信息查询功能。

该功能提供了动车和高铁的列车检票口的信息，正好圆了自己的之前挖的坑。

[微信公众号地址]: http://kf.cd-rail.com/CTKF/CTZX/view/mainFrame/wx_hcxx.html

## 如何获取接口

链接地址：

[车站查询]: http://kf.cd-rail.com/CTKF/CTZX/view/mainFrame/wx_hclbdetail.html?zmlm=ICW&amp;amp;station=%E6%88%90%E9%83%BD%E4%B8%9C&amp;amp;train_no=&amp;amp;type=cfhc
[车次查询]: http://kf.cd-rail.com/CTKF/CTZX/view/mainFrame/wx_hclbdetail.html?zmlm=CUW&amp;amp;amp;station=%E9%87%8D%E5%BA%86%E5%8C%97%E7%AB%99&amp;amp;amp;train_no=258&amp;amp;amp;type=cfhc

chrome F12 点击查询在Network中找到相关接口的信息

http://www.cd-rail.com:9090/CTKF/GeneralProServlet?code=C50101&login=["10.192.111.79","hhs","hhs"]&sql=["20170703","ICW"]&where=[]&order=[]

通过*[Postman]()*模拟数据请求获取数据获取信息请求的格式


## 接口参数

1. 车站查询


   | 参数名称  | 参数含义 | 参数示例                          |
   | :----- | :---- | :----------------------------- |
   | code  |      | C50101                        |
   | login | 登陆信息 | ["10.192.111.79","hhs","hhs"] |
   | sql   | 查询语句 | ["20170703","ICW"]            |
   | where |      | []                            |
   | order |      | []                            |

2. 车次查询


   | 参数名称  | 参数含义 | 参数示例                          |
   | :----- | :---- | :----------------------------- |
   | code  |      | C5010                         |
   | login | 登陆信息 | ["10.192.111.79","hhs","hhs"] |
   | sql   | 查询语句 | ["20170703","8503","ICW"]     |
   | where |      | []                            |
   | order |      |                               |

3. 车站名称查询


   | 参数名称  | 参数含义 | 参数示例                          |
   | :----- | :---- | :----------------------------- |
   | code  |      | C50102                        |
   | login | 登陆信息 | ["10.192.111.79","hhs","hhs"] |
   | sql   | 查询语句 | []                            |
   | where |      | []                            |
   | order |      | []                            |


## 解析接口返回结果

```json
[{"CHECK_STATUS":"停止检票","CHECK_TICKET":"A10、A11","END_CHECK_TIME":"2017/07/04 09:30:00","END_STN":"重庆北","END_STN_CODE":"CUW","IN_DATE":"2017/07/04 18:59:52","START_CHECK_TIME":"2017/07/04 09:14:00","START_STN":"成都东","START_STN_CODE":"ICW","STATUS_TRAIN":"正点","STN_CODE":"ICW","TD_DATE_ARR":"00:00:00","TD_DATE_DEP11":"09:33","TRAIN_DEP":"G8503","WAIT_ROOM":"2层候车区","WGQBZ":"0"}]
```

## Python

1. urlib、urlib2
   - 构建Header，模拟Post请求
2. prettytable
   - 处理返回数据格式化为表格
3. re
   - 匹配站点名称以及检索结果

## 展示

![TIM截图20170704212154](http://orj5hqpmw.bkt.clouddn.com/find_train_check_in.png)

