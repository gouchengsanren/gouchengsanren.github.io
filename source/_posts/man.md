---
title: man
date: 2020-04-12 06:56:37
tags:
---

记录man的基础知识，常用的指令、系统调用等等的说明。
<!--more-->

`man man`
```
       1   Executable programs or shell commands
       2   System calls (functions provided by the kernel)
       3   Library calls (functions within program libraries)
       4   Special files (usually found in /dev)
       5   File formats and conventions eg /etc/passwd
       6   Games
       7   Miscellaneous (including macro packages and conventions), e.g. man(7), groff(7)
       8   System administration commands (usually only for root)
       9   Kernel routines [Non standard]
```
man这本书有9本（默认从第1本开始找，依次往后）：
* 第一本讲系统指令
比如 `man ascii` 、`man sed` 等
```
SED(1)                                            User Commands                                            SED(1)

NAME
       sed - stream editor for filtering and transforming text
```
左上角的 `SED(1)` 这个1就是第几本。
* 第二本讲系统调用
比如 `man 2 open` 、 `man select` 等
如果 `open` 不指定 `2` ，那么 `man` 默认查询的是 `openvt`。
而 `select` 在第一本书中没有类似的。
* [FIX ME]
<br>
