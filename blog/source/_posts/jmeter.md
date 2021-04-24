---
title: jmeter学习记录
date: 2019-05-12 22:24:21
tags:
    - 性能测试
    - jmeter
    - 工具使用
categories:
    - 学习
    - 性能测试
description:  jmeter使用笔记
---

# jmeter使用基础
主页面基本介绍：
{% asset_img commonView.png commonView %}

## 命令行执行

    jmeter -n -t F:\jmeter\TestPlan.jmx -l F:\jmeter\TestPlan.csv

## 参数处理
* 手动填写
* 读取csv，添加CSV Data Set Config 
{% asset_img csvSetConfig.png csvConfig %}

* 读取文件

## 内置变量
- 日志函数：`${__log(“message here”)}`
- 时间函数 time：`${__time(format)}`
- 求和函数 intSum：`${__intSum(2,3,result)}` 可以调用result变量

## selenium脚本录制
* chrome录制依赖BlazeMeter插件，录制完成导出成jmx格式即可
* fireFox可基于selenium+jmeter插件完成录制（适用于低版本fireFox）
* 一个支持fireFox的脚本录制插件：katalon recorder（不支持导出为jmx形式）

## 性能测试用例设计
流程：

    线程组（用例名称），samplers, 逻辑控制器，配置元件，定时器，前置处理器，监听和断言。

## 测试报告
csv报告使用参数：

    -l F:\jmeter\TestPlan.csv
html报告使用参数：

    -l F:\jmeter\TestPlan.csv -e -o F:\jmeter\httpReports



## 问题
- http请求生成的csv测试报告，包括一个请求中途重定向等所有路径，csv中看到的请求数量可能是最真实的。
- 从csv读取参数时，用excel创建的csv文件无法读取参数,实际使用notepad++创建的csv文件没有问题。
- **性能测试难点在于场景模拟，主要维度：**
    1. 有思考时间：用户在不同的操作之间有时间停顿，或者延迟就是模拟用户的操作过程。
    2. 步伐、速度，主要是大量用户进场和退场的时间，控制迭代之间的时间。
    3. 压测时间
    4. 业务场景
- 使用Aggregate Graph（聚合图像报告）抛出了如下异常，[stackoverflow][]给出的答案是5.1版本会修复但是并没有，建议使用http report
    
        2019-05-11 22:30:01,456 ERROR o.a.j.JMeter: Uncaught exception: 
        java.lang.ArrayIndexOutOfBoundsException: null



[stackoverflow]: https://stackoverflow.com/questions/52755372/unable-to-display-aggregate-graph-within-jmeter
