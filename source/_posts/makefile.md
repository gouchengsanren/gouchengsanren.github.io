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
### 延时变量和即时变量
makefile中的赋值分为两种：
* 1）延时变量；
     在执行时才确定。
* 2）即时变量；
     立即确定。

例：
```shell
a'makefile:
  1 VAR = $@
  2 VAR2 := $@
  3 $(warning '$(VAR)','$(VAR2)')
  4                      
  5 a:                   
  6     @echo \'$(VAR)\',\'$(VAR2)\'
```
执行：
```shell
chuck@chuck11:a$ make
makefile:3: '',''
'a',''
```
`VAR` 是延时变量，在执行命令时才确定。
`VAR2` 是即时变量，写时是多少就是多少。

> 那么gnu make是怎么规定哪些是延时变量，哪些是即时变量呢？

|举例|说明|
|-|-|
|VAR = xxx|延时变量|
|VAR ?= xxx|延时变量|
|VAR := xxx|即时变量|
|VAR += xxx|跟随原有定义，如果原来VAR是延时变量，那么现在还是延时变量，否则还是即时变量|

### 变量的导出（export）
我们在编译时，目录通常是切来切去的（通过make -C）。
*注：*
*关于 `-C` ，请参考[参数](#ARCHOR_参数)一节*
一个makefile里的变量是没法在其他makefile中使用的，如果要使用，就要用 `export` 将它导出。

例：
```shell
├── a
│   └── makefile
├── b
│   └── makefile

a's makefile:
  1 a:
  2     @echo \'$(VAR)\'

b's makefile:
  1 VAR ?= iamvar
  2 
  3 b:
  4     make -C ../a/
```
b目录下执行make：
```shell
chuck@chuck11:b$ make
make -C ../a/
make[1]: Entering directory '/home/chuck/bsp/a'
''
make[1]: Leaving directory '/home/chuck/bsp/a'
```
修改b makefile，将 `VAR` 导出：
```shell
  1 VAR ?= iamvar
  2 export VAR
  3 
  4 b:
  5     make -C ../a/
```
执行：
```shell
chuck@chuck11:b$ make
make -C ../a
make[1]: Entering directory '/home/chuck/bsp/a'
'iamvar'
make[1]: Leaving directory '/home/chuck/bsp/a'
```
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

### shell
`$(shell xxx)`

### filter / filter-out
#### filter
`$(filter pattern, xxx)`
把xxx中符合pattern的<font color=red>**过滤出来**</font>。

例：
```shell
  1 obj-y := a.o b.o c/ d/
  2 DIR := $(filter %/, $(obj-y))
  3 NDIR := $(filter-out %/, $(obj-y))
  4 
  5 a:
  6     @echo $(DIR)
  7     @echo $(NDIR)
```
执行：
```shell
chuck@chuck11:a$ make
c/ d/
a.o b.o
```

#### filter-out
`$(filter-out pattern, xxx)`
把xxx中符合pattern的<font color=red>**过滤掉**</font>。

### patsubst
`$(patsubst pattern, replacement, xxx)`
把xxx中符合pattern的替换成replacement。
patsubst这个名字不好记。

例：
```shell
  1 obj-y := a.c b.o c/ d/
  2 VAR := $(patsubst %/, %, $(obj-y))
  3 VAR := $(patsubst %.c, %.o, $(VAR))
  4                              
  5 a:
  6     @echo $(VAR)
```
执行：
```shell
chuck@chuck11:a$ make
a.o b.o c d
```
<br>


## 假目标（.PHONY）
我们makefile中会有写 `clean` 目标，用来清除编译环境。
如果目录下恰巧有一个 `clean文件` ，那么make就会不执行：
```shell
b's makefile:
  1 VAR ?= iamvar
  2 export VAR
  3 
  4 b:
  5     make -C ../a/
  6 
  7 clean:
  8     @echo "clean"
```
执行：
```shell
chuck@chuck11:b$ >clean
chuck@chuck11:b$ make clean
make: 'clean' is up to date.
```
这种情况，我们需要告诉make，这个target指定时，一定要被执行。
使用 `.PHONY` （phony的意思是‘假的’）:
```shell
  1 VAR ?= iamvar
  2 export VAR
  3 
  4 .PHONY: clean               //指定哪些是虚假目标
  5        
  6 b:
  7     make -C ../a/
  8 
  9 clean:
 10     @echo "clean"
```
执行：
```shell
chuck@chuck11:b$ make clean
clean
```
<br>


## <a name="ARCHOR_参数">参数</a>

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
<br>


## 通用makefile
参考韦老师的通用makefile写了一个。
[通用makefile](https://github.com/gouchengsanren/files/tree/master/general_makefile)

简单说两句。
目录结构：
```shell
.
├── a.c
├── include
│   ├── a.h
│   └── sub_a.h
├── main.c
├── makefile                    //当前目录makefile
├── makefile.build              //会去包含当前目录的makefile
└── sub
    ├── makefile                //当前目录makefile，当然对于根目录，它是字目录
    └── sub_a.c
```

学到1点：
* 目标永远是在make读完所有配置后才去执行，目标写前面也不会先执行

执行：
```shell
chuck@chuck11:general_makefile$ make
make -C ./ -f /home/chuck/github/gouchengsanren/files/general_makefile/makefile.build
make[1]: Entering directory '/home/chuck/github/gouchengsanren/files/general_makefile'
make -C sub -f /home/chuck/github/gouchengsanren/files/general_makefile/makefile.build
make[2]: Entering directory '/home/chuck/github/gouchengsanren/files/general_makefile/sub'
gcc -Wall -O2 -g -I /home/chuck/github/gouchengsanren/files/general_makefile/include -D DEBUG -D SUBA_VAL=10 -Wp,-MD,.sub_a.o.d -c -o sub_a.o sub_a.c
ld -r -o built-in.o sub_a.o
make[2]: Leaving directory '/home/chuck/github/gouchengsanren/files/general_makefile/sub'
gcc -Wall -O2 -g -I /home/chuck/github/gouchengsanren/files/general_makefile/include   -Wp,-MD,.main.o.d -c -o main.o main.c
gcc -Wall -O2 -g -I /home/chuck/github/gouchengsanren/files/general_makefile/include   -Wp,-MD,.a.o.d -c -o a.o a.c
ld -r -o built-in.o main.o a.o sub/built-in.o
make[1]: Leaving directory '/home/chuck/github/gouchengsanren/files/general_makefile'
gcc -o app built-in.o 
app has been built!
```
过程：
* 1）编译app，build-in.o
* 2）build-in.o，需要sub/build-in.o main.o a.o
* 3）对于sub/build-in.o，它因为没有子目录，所以只需要sub_a.o

思想很简单，makefile不好写。







