---
title: shell
date: 2020-04-12 06:50:50
tags:
categories: linux
---

记录shell的基本语法、常用的使用技巧等。
<!--more-->

**不一定非得写shell脚本再执行，可以直接执行**
`for((i=0;i<10;i++));do echo $i;done`
<br>
**xargs cp结合使用**
`find . -name "*" | xargs -i cp {}  /home/users/`
<br>
**删除除了某个/某些文件外的其他文件**
```
>a1.c
>a2.c
>a3.c

rm -rf !(a1*|a3*)
```
结果就是，保留了 `a1.c`、 `a3.c`，删除了 `a2.c` 。
<br>
