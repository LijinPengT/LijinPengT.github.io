---
title: GET/POST的区别
date:  2020-03-23 21:09:38 +0800
category: 计算机网络
tags: basic
excerpt: 深入理解GET/POST的区别
---

# 深入理解GET/POST的区别

## GET/POST的基本区别

+ 最直观的就是GET把参数放在URL里, POST通过request body传递参数

+ "常见答案"(参考w3cschool)
    + GET在浏览器回退时是无害的, 而POST会再次提交请求
    + GET产生的URL地址可以被Bookmark, 而POST不可以
    + GET请求会被浏览器互动cache, 而POST不会, 除非手动设置
    + GET请求只能进行URL编码, 而POST支持多种编码方式
    + GET请求参数会被完整保留在浏览器历史记录里, 而POST中的参数不会被保留
    + GET请求在URL中传送的参数是有限长度限制的, 而POST没有
    + 对参数的数据类型, GET只接受ASCII字符, 而POST没有限制
    + GET比POST更不安全, 因为参数直接暴露在URL上, 所以不能用来传递敏感信息
    + GET参数通过URL传递, POST放在Request body中

## GET/POST本质区别
+ 本质上就是没区别

## GET/POST是什么?
+ HTTP协议中的两种发送请求的方法

## HTTP是什么?
+ HTTP是基于TCP/IP的关于数据如何在万维网中通信的协议.
+ HTTP底层是TCP/IP, 所以GET/POST的底层也是TCP/IP, 两者都是TCP链接, 技术上两者发送请求的方式交换是可以的.

## 形象化协议
+ TCP比作汽车
+ HTTP比作为交通规则(服务类别: GET/POST/PUT/DELETE)
+ 执行GET请求的时候, 给汽车贴上GET标签(设置METHOD=GET), 而且要求把传送的内容放在车顶(URL上)
+ 执行POST请求, 给汽车贴上POST标签, 货物放在车里面(request body中)
+ 也可以在GET请求中在车厢里放点内容, POST的时候也可以在URL上放一些数据(今天面试的时候, 我说post应该不能把参数方url里面吧, 现在打脸了)
+ HTTP是行为准则, TCP才是GET/POST实现的基本

## HTTP对GET和POST的参数传递方式提出了要求, 那么参数大小限制怎么说?
+ 不同的浏览器, 服务器: 运输公司
+ 理论上可以URL上无限加参数, 但是浏览器和服务器会限制单次传输数据的大小, 通常浏览器都会限制URL长度在2K字节, 服务器最多处理64K大小的URL, 超过的部分, 不会进行处理
+ 如果使用GET服务, 在Request body里面存了数据, 不同服务器处理方式不同, 有的会读取数据, 有的直接忽略, 所以不能保证一定能被接受

> GET和POST本质上就是TCP链接，并无差别。但是由于HTTP的规定和浏览器/服务器的限制，导致他们在应用过程中体现出一些不同

## GET和POST的重大区别
+ GET产生一个TCP数据包
    + GET方式请求, 浏览器会把http header和data一并发送出去, 服务器响应200(返回数据)
+ POST产生两个TCP数据包
    + POST方式的请求, 浏览器先发送header, 服务器响应100 continue, 浏览器再发送data, 服务器响应200 ok(返回数据)
+ POST需要两步, 时间上消耗要多一点, 看起来GET比POST更有效, 因此Yahoo推荐GET替换POST来优化网站性能, 但这是个坑
    + GET/POST都有自己的语义, 不能随便混用
    + 良好的网络环境下, 发送一次包和两次包的时间基本可以无视, 而网络差的时候, 两次包的TCP在验证数据包的完整性上有非常大的优势.(还好最近的开发的后台接口都给整成POST了,hhhh)
    + 并不是所有浏览器都会在POST中发送两次包, Firefox就只发送了一次

参考链接: [GET和POST两种基本请求方法的区别](https://www.cnblogs.com/logsharing/p/8448446.html)
