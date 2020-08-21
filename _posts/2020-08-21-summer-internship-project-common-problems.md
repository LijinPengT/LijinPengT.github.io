---
title: 2020暑期实训项目常见问题
date:  2020-08-21 20:28:20 +0800
category: 后端
tags: Python
excerpt: 项目开发常见问题
---

# 2020暑期实训项目常见问题

## OpenDaylight

+ 在/home/openlab/sdn-qos/flowmanager-karafxxx/bin/ 里面执行启动OpenDaylight

```shell
./karaf
```

## 项目创建

+ 项目创建问题可以百度，或者看之前的教程

## 前端

### 页面展示问题

+ 静态资源引入问题
    + css，js文件的引入，请百度或者看之前的教程，不引入可能前端数据无法获取，无法请求url
+ 表单里面input元素的id或者name一定要和js文件里面保持一致
+ 流表管理，meter表管理部分的数据展示，要使用for循环
    + 流表，meter表管理部分的表格tbody部分代码，需要自己参考项目流程简介补全
    ![flowtable](https://s1.ax1x.com/2020/08/21/dN0zIe.png)
    ![metertable](https://s1.ax1x.com/2020/08/21/dN0xaD.png)

### js文件

+ 最后成不成功都alert出来，防止以为没有反应，其实后台已经报错
+ 多注意按F12查看console里面的错误信息
+ js文件里面的ajax请求路径和数据格式要注意
+ 一般前端说请求失败
    + 一是请求url错误
    + 二是，后端的问题

## 后端

### Models.py

+ makemigrations，migrate出错，注意查找报错信息，一大堆错误里面只有很少的关键信息
+ 注意定义数据模型

### views.py

+ 注意缩进问题，注意获取的数据的处理和odl函数的调用

### odl.py

+ 注意添加流表和meter表的格式，和数据属性的正确性
+ 如果前面没有问题，而后面失败了，只可能是属性少了或者错了
+ 也可能是前面的数据填写的不规范
    + 源ip，目的ip要填控制器ip和交换机ip，顺序可以互换

## 最后

+ 出现报错，一定自己先看报错信息最关键的部分，一般都与自己的文件有关
+ 除了流表下发的问题，其他基本可以靠百度解决

[项目二流程](https://lijinpengt.github.io/后端/2020/08/19/summer-internship-project-two.html)

