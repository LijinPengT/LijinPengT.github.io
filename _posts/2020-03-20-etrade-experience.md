---
title: 智能电网交易系统
date:  2020-03-20 14:13:33 +0800
category: blockchain
tags: zk-SNARK
excerpt: 论文中实验数据的测试和整理
---

# 智能电网交易系统数据测试整理

## 一、隐匿传输模块

+ [隐匿传输实验数据图](https://conquerit.top/ecroTrans.html)

### 隐匿传输固定开销模块测试

+ 发送方发送交易信息(公钥, 信息, 密文)
+ 接收方接收交易信息测试(计算秘钥, 解密密文)

### 隐匿传输非固定开销模块测试

+ 接收方计算并匹配隐匿标签(每次随机模拟印尼标签命中时间, 测试一百次, 重复三组)

## 二、零知识证明模块

### 链下计算模块

+ 五个步骤
  + compile
  + setup
  + compute-witness
  + generate-proof
  + export-verifier
+ 三种方案, 每种方案的每个步骤分别测试一百次并绘制曲线
+ 实验数据使用echarts绘制, [零知识证明实验数据图](https://conquerit.top/zokrates.html)

### 链上验证模块

+ 将export-verifier阶段导出的智能合约, 部署在以太坊上
+ 编写合约调用JavaScript脚本, nodejs执行脚本
+ 由于直接for循环调用太快,测试数据误差巨大,所采用setInterval(func(), 2000)
+ 当测试100次之后调用clearInterval(timer),停止验证,并打印存储测试数据的数组

## 三、整个方案总时长

+ 首先对三次隐匿传输的时长取平均值
+ 然后将三种零知识证明方案与隐匿传输平均值分别结合形成三种方案
+ 对三种方案绘制时间曲线
+ [总时长数据曲线](https://conquerit.top/total.html)