---
title: linux启动流程
date: 2020-04-12 06:56:52
tags:
categories: linux
---

梳理linux的启动流程。
<!--more-->

```
windows             linux
    bios                bootloader(uboot)
    | 启动              | 启动
    windos              linux内核(内核、驱动)
    | 识别              | 识别
    c盘                 根文件系统(自带的app、我们的app)
    | 运行              | 启动
    app                 app
```
<br>
