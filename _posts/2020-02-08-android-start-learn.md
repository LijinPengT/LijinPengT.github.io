---
title: Android allowBackup属性
date:  2020-02-08 14:06:50 +0800
category: Android
tags: 移动端安全问题
excerpt: android:allowBackup问题
---

## 介绍

    从 API 8以来，谷歌为用户提供了应用程序数据备份和恢复的功能。此功能的开关是AndroidManifest.xml 文件的 allowBackup属性，默认为true开启。所以用户可以对我们的应用数据备份。当allowBackup标志为true时，用户即可通过adb backup和adb restore来进行对应用数据的备份和恢复，这可能会带来一定的安全风险。
    手机在我们通过USB调试连接数据的时候会弹出风险提示框，就是考虑到了安全风险。Android属性allowBackup安全风险源于adb backup容许任何一个能够打开USB 调试开关的人从Android手机中复制应用数据到外设，一旦应用数据被备份之后，所有应用数据都可被用户读取；adb restore容许用户指定一个恢复的数据来源（即备份的应用数据）来恢复应用程序数据的创建。因此，当用户的数据被备份了，那么他就可以将这个数据通过备份恢复到其他的手机或者模拟器上。这对用户来说是不被允许的。因此为了安全起见，开发者务必将allowBackup标志值设置为false来关闭应用程序的备份和恢复功能，以免造成信息泄露和财产损失

## allowBackup的风险控制方法

1. android:allowBackup="false"
2. 通过手机设备的IMEI号来辨识来设备编号和备份前是否一致，不一致则直接跳转登陆页面的同时清空当前应用数据及缓存
3. allowBackup的值为false 对项目运行没有任何影响.