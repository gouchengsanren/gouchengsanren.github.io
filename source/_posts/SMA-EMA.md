---
title: SMA/EMA
categories: 交易
date: 2020-10-08 05:41:29
mathjax: true
tags:
---

SMA和EMA的公式和含义。
> 注，我用的行情软件是老虎，公式都是对应老虎里的。
<!--more-->
<br>

## SMA
即大多数软件里的MA，简单移动平均。
所有人都知道，简单归简单，还是把它写出来。
### 公式
<p align="left">
$$\begin{aligned}
MA=\frac{\sum_{i=1}^N{CLOSE_i}}{N}
\end{aligned}$$
</p>

### 含义
最近N天的收盘价相加除以N。

它有缺点，《以交易为生》一书中也提到过：
`SMA会报警两次（看门狗会叫两次）`。今天的叫一次，N日前的价格被去掉了，也叫了一次。
它的公式，让它计算了N日前的收盘价（这个价格实际上影响很小）。
不过它仍然是重要的指标，因为用的人多。
雷公包括我看的几本书的作者都强调，我们赚的是趋势的钱。不用管趋势的对错，只需要跟着趋势走。
<br>


## EMA
指数移动平均。
SMA的改良版，越近的交易日，它的收盘价占比更大。它显然比MA合理（单纯从指标的定义上讲）。
### 公式
<p align="left">
$$\begin{aligned}
EMA&=CLOSE*K+REF(EMA,1)*(1-K)\\
K&=\frac{2}{N+1}
\end{aligned}$$
</p>

### 含义
当天收盘价占比K，前一天的EMA占比（1-K）。这样一来，越接近今天的收盘价占比越重。

> 雷公做了简单的总结
价在线上，线向上
价在线下，线向下
所见即所得

从公式定义看，貌似雷公讲的不对，因为EMA只是当天收盘价占比高而已，如果N比较大，那么当天收盘价的影响就很小，并不一定使EMA均线改变方向。
**但从历史数据看，EMA5和EMA20我找不到反例!**
找不到反例，截止现在它就是真理。直到市场扇我一巴掌。

**交易计划中，只应该做SMA和EMA双均线都拐头向上的行情。**
k线图中，应该同时标出MA和EMA。以及20和60的抵扣价。（抵扣价在老虎中一直找不到好方法，只能通过REFX全部画出来）

<br>

