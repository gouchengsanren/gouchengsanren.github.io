---
title: ubuntu问题汇总
date: 2020-04-12 01:00:18
tags:
categories: linux
---

记录常见的ubuntu使用过程中的问题，包括一些配置等。
<!--more-->

## A start job is running for wait for network to be Configured
ubuntu 18.04
开机时会卡在这一步，原因应该是网络不通。
解决方式：
`vim /etc/systemd/system/network-online.target.wants/systemd-networkd-wait-online.service`
在[Service]标签下增加：
`TimeoutStartSec=2sec`
<br>




