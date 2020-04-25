---
title: makefile
categories: compile
date: 2020-04-24 23:41:38
tags:
---

介绍makefile的语法等。
<!--more-->

## GNUMAKE
大佬给我们翻译了make使用手册，非常感谢。
[gnumake](https://github.com/gouchengsanren/files/blob/master/gunmake.htm)(下载后浏览器打开)
下面会介绍常用语法。
<br>

## 规则
```
目标（target）：依赖（prerequiries）
<tab>命令（command）
```
make的思想简单粗暴：
`目标` 文件还没有，或者 `依赖` 文件有更新，就执行 `命令` 。
*所以，make并不是一个编译工具，所有符合上述使用场景的，都可以使用make。
只是我们项目中的make都用来编译了，让我们误以为make只能用来编译。*

Note：
* 我们make常常遇到莫名的错误，先检查格式问题，`tab` 是否对，有没有错误的
以 `tab` 打头的空行等等
* `make` 会用 `shell` 执行 `command` 。所以command必须是手动在shell下执行
没有问题的。
<br>


## 变量
<font color=red size=4>[TODO]</font>
<br>


## 函数
介绍makefile中常用的函数。

### foreach
`$(foreach i, list, newi)`
遍历 `list` 中的每一项元素（`i`），将其改成 `newi` 的形式。

### wildcard
`$(wildcard pattern)`
把符合 `pattern` 描述的文件都列出来。
pattern可以是切实的文件：
```
a = $(wildcard a)
检查a是否存在，存在还是a（它自己），不存在就是空。
这里的a也可以是列表。
```
也可以是通配符：
```
a = $(wildcard *.c)
```

## 参数

### make -f
`-f` 选项用来指定makefile。
默认情况下，是使用当前目录下的makefile。
例：
```shell
├── a
│   └── makefile
├── b
│   └── makefile

a's makefile:
  1 $(warning $(shell pwd))

b's makefile:
  1 $(warning $(shell pwd))    
  2 
  3 b:
  4     make -f ../a/makefile
```
执行：
```shell
chuck@chuck11:b$ make
makefile:1: /home/chuck/bsp/b                   //打出了b makefile warning
make -f ../a/makefile                           //执行第一个target
make[1]: Entering directory '/home/chuck/bsp/b' //进入b目录
../a/makefile:1: /home/chuck/bsp/b              //使用a makefile，a makefile warning打出，
                                                  因为当前目录是b，所以打出的是b目录
make[1]: *** No targets.  Stop.
make[1]: Leaving directory '/home/chuck/bsp/b'
makefile:4: recipe for target 'b' failed
make: *** [b] Error 2
```
我们给a makefile加2个目标：
```shell
  1 $(warning $(shell pwd))    
  2 
  3 a:
  4     @echo aaa
  5 
  6 b:
  7     @echo bbb
```
执行：
```shell
chuck@chuck11:b$ make
makefile:1: /home/chuck/bsp/b
make -f ../a/makefile
make[1]: Entering directory '/home/chuck/bsp/b'
../a/makefile:1: /home/chuck/bsp/b
aaa         //执行的第一个目标a，不是b。其实，make -f ../a/makefile的效果和将a的makefile拷贝
              到当前目录，再执行make是一样的。没有目标传递的说法。
make[1]: Leaving directory '/home/chuck/bsp/b'
```

### make -C
`-C` 指切换到其他目录。
我们编译驱动就会指定 `-C` 为内核根目录。
还是使用上面小结的a、b的makeifle。
修改一下：
```shell
  1 $(warning $(shell pwd))    
  2 
  3 b:
  4     #make -f ../a/makefile
  5     make -C ../a/
```
执行：
```shell
chuck@chuck11:b$ make
makefile:1: /home/chuck/bsp/b
#make -f ../a/makefile
make -C ../a/
make[1]: Entering directory '/home/chuck/bsp/a'     //目录变了，执行-C的效果和cd到a目录，再执行
                                                      make效果是一样的。
makefile:1: /home/chuck/bsp/a
aaa
make[1]: Leaving directory '/home/chuck/bsp/a'
```








