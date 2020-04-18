---
title: gcc
date: 2020-04-12 08:14:49
tags:
---

gcc的一些基础知识。
编译到底做了一些什么，编译时的注意事项等等。
<!--more-->

**头文件的位置**
像 `stdio.h` 在编译器中的 `include` 目录。
自己需要的头文件，编译时用 `-I` 指定，比如 `-I ./`
<br>

**库的位置**
像 `printf` 函数，都在事先编好的库中。库在编译器中的 `lib` 目录。
编译时，
* 用 `-lxxx` 指定需要的库，编译器会去 `lib` 、 `usr/lib` 目录下找叫 `libxxx.so`
或者 `libxxx.so.数字` 的库文件。
* 用 `-L xxx` 指定库的路径。
<br>
<br>

**GCC的使用**
韦老师团队写的下面这篇文档非常好。
[01.嵌入式Linux应用开发基础知识.docx](https://github.com/100askTeam/01_all_series_quickstart/blob/master/04_%E5%BF%AB%E9%80%9F%E5%85%A5%E9%97%A8_%E6%AD%A3%E5%BC%8F%E5%BC%80%E5%A7%8B/01_%E5%B5%8C%E5%85%A5%E5%BC%8FLinux%E5%BA%94%E7%94%A8%E5%BC%80%E5%8F%91%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86/doc_pic/01.%E5%B5%8C%E5%85%A5%E5%BC%8FLinux%E5%BA%94%E7%94%A8%E5%BC%80%E5%8F%91%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86.docx)

最初的程序是程序员对着巨大的机器插拔线缆。
之后有了纸带打孔编程，指代上的洞代表01，一条纸带就是要给程序。
再之后，就用一段特定的代码来表示一段特定的01串。这就是汇编语言。
再往后，就出现了c等高级语言。



