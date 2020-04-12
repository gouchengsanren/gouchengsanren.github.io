---
title: shell
date: 2020-04-12 06:50:50
tags:
---

记录shell的基本语法、常用的使用技巧等。
<!--more-->

**不一定非得写shell脚本再执行，可以直接执行：**
`for((i=0;i<10;i++));do echo $i;done`
<br>
**xargs cp结合使用：**
`find . -name "*" | xargs -i cp {}  /home/users/`
<br>
