---
title: 裸机led
categories: uboot
date: 2020-11-15 09:03:45
tags:
---

imax6ull led裸机程序。
<!--more-->

## 烧写-验证
烧写用100ask的烧写工具。
用专业版。
基础版只能烧写到emmc，但是不能运行。得把拨码调成emmc启动，才能看到效果，拨来拨去效率太低。
用专业版可以直接运行（不烧写，直接运行），方便很多。

工具只支持imx格式的，所以必须得做成imx格式。
通过以下命令：
```shell
./tools/mkimage -n ./tools/imximage.cfg.cfgtmp -T imximage -e 0x80100000 -d led.bin led.imx
```
含义不明，先会用。
<br>

## Makefile
先看makefile怎么写。
```c
CROSS_COMPILE=arm-linux-gnueabihf-
CC=$(CROSS_COMPILE)gcc
LD=$(CROSS_COMPILE)ld
AR=$(CROSS_COMPILE)ar
OBJCOPY=$(CROSS_COMPILE)objcopy

led.imx:start.S led.c main.c
	$(CC) -g -c -o start.o start.S
	$(CC) -g -c -o led.o led.c
	$(CC) -g -c -o main.o main.c
	$(LD) -T imx6ull.lds -g start.o led.o main.o -o led.elf
	$(OBJCOPY) -O binary -S led.elf led.bin
	./mkimage -n imximage.cfg.cfgtmp -T imximage -e 0x80100000 -d led.bin led.imx

clean:
	rm -rf *.dis *.bin *.elf *.imx *.img *.o
```
这边有几行暂时看不懂。
* ld -T imx6ull.lds，指定了一个连接文件，最后生成的文件格式为elf，elf又是什么格式
* objcopy到底干嘛的
* mkimage就更看不懂了

<br>

## led c实现
就是c代码，和内核下没什么区别。
但有一个疑问：
```c
static volatile unsigned int *ccm_ccgr1;
ccm_ccgr1 = (volatile unsigned int *)0x20c406c;
*ccm_ccgr1 |= (3<<30);
```
> 为什么可以直接寄存器地址取址，然后操作就是写寄存器操作？？

## 汇编部分
一个c程序要跑起来，总得初始化c的堆栈吧，但是这个是裸机啊。它这个堆栈的初始化在哪里？
示例程序中有个start.S，是这个吗？
```c
.text
.global  _start

_start: 				
	ldr  sp,=0x80200000
	bl clean_bss
	bl main

halt:
	b  halt 

clean_bss:
	ldr r1, =__bss_start
	ldr r2, =__bss_end
	mov r3, #0
clean:
	str r3, [r1]
	add r1, r1, #4
	cmp r1, r2
	bne clean
	
	mov pc, lr
```
看不懂。
<br>

## 不用c，直接用汇编实现这个led程序
报错：
```shell
led.S:9: Error: only D registers may be indexed -- `str r1 [r0]'
```
对应的代码是：
```c
    /* enable gpio5 */
    ldr r0, =0x020c406c
    ldr r1, =0xC0000000
    str r1 [r0]
```
原因是语法错误，`str r1, [r0]` 少个逗号。
整个程序：
```c
.text
.global _start


_start:
    /* enable gpio5 */
    ldr r0, =0x020c406c
    ldr r1, =0xC0000000
    str r1 [r0]

    /* iomux as gpio */
    ldr r0, =0x02290014
    ldr r1, =5
    str r1 [r0]

    /* set output */
    ldr r0, =0x020ac004
    ldr r1, =8
    str r1 [r0]

    /* 拉低点亮led */
    ldr r0, =0x020ac000
    ldr r1, =0
    str r1 [r0]

halt:
    b halt
```
遗憾的是，并没有点亮led。
和start.S差别挺大的。
既然单独写的不行，那就把这些代码放到start.S中。
还是不行。
只有示例才可以。
看样子必须得学下汇编，否则和瞎子没有两样。
<br>




