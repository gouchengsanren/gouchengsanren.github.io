---
title: linux目录结构
date: 2020-04-12 06:51:11
tags:
---

介绍linux的目录结构。
<!--more-->

```
linux目录结构
|-bin           基本命令，所有用户可使用（与开机有关，就是系统启动要用的）
|-boot          启动文件，比如内核等
|-dev           设备文件
|-etc           配置文件
|-home          家目录
|-lib           库（/bin /sbin下的app用的库，/usr/bin /usr/sbin下的也可以放这里，没有限制）
|-media         插上u盘等外设时会挂在到该目录
|-mnt           用来挂在其他文件系统
|-opt           可选程序
|-proc          挂载虚拟的proc文件系统，可以查看各进程信息（proc，其实就是process，但现在
                proc内容很多了
|-root          root用户的家目录
|-sbin          基本的系统命令，系统管理员才能使用（与开机有关，就是系统启动要用的）
|-sys           用来挂载虚拟的sys文件系统，可查看系统信息
|-tmp           临时目录，存放临时文件
|-usr           unix software resource，存放可分享的与不可变动的数据（usr和var是一对，
                usr的不可变动指开机后还在的。相对的，var是变动的，开机后就没了）
|   |-bin       绝大部分用户可使用的指令（与开机无关）
|   |-include   头文件
|   |-lib       库
|   |-local     系统管理员在本机自行安装、下载的软件
|   |-sbin      非系统正常运行所需要的系统命令
|   |-share     放置共享文件的地方，比如/usr/share/man里存放帮助文件
|   |-src       源码
|-var           主要针对常态性变动的文件，包括缓存（cache）、log文件等
```

