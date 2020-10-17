---
title: RSI
categories: 交易
date: 2020-10-17 07:13:54
mathjax: true
tags:
---


RSI指标的公式和含义。
> 注，我用的行情软件是老虎，公式都是对应老虎里的。
<!--more-->
<br>

### 含义
直译是相对强度指标。
用的相对少，包括雷公也是没见过他用这个指标。

特点：
* `震荡`指标
* 只研究`收盘价`
* `不滞后`
* 研究的是`价格的波动`，注意`不是涨跌幅的波动`。是价格的绝对值。


### 公式
> 老虎自带公式

````c
//前一个交易日的收盘价
LC:=REF(CLOSE,1);
//如果涨了，temp1就是涨的值，否则就是0
TEMP1:=MAX(CLOSE-LC,0);
//temp2，今日和前一个交易日收盘价差值的绝对值
TEMP2:=ABS(CLOSE-LC);
//也就是说：
//如果涨了，temp1=temp2等于C-LC
//如果跌了，temp1=0，temp2等于LC-C

//P1:6, P2:12, P3:24
//RSI：temp1的EMA（只不过这里权重系数的分子是1，也就是原来是2/(N+1)，现在是1/(N+1)，权重调小了
RSI1:SMA(TEMP1,P1,1)/SMA(TEMP2,P1,1)*100,COLORFFFFFF;
RSI2:SMA(TEMP1,P2,1)/SMA(TEMP2,P2,1)*100,COLORFF00FF;
RSI3:SMA(TEMP1,P3,1)/SMA(TEMP2,P3,1)*100,COLOR00FFFF;
````
这个指标比较难理解。
我们举个例子看，取RSI1,6日。
如果上一个交易日rsi=50，那么SMA(TEMP1,6,1)/SMA(TEMP2,6,1)=0.5，假设分别是0.5和1好了，简单点。
假设今天是涨的，涨了1，那么temp1=temp2=1.
即：
<p align="left">
$$\begin{aligned}
RSI&=\frac{SMA(TEMP1,6,1)}{SMA(TEMP2,6,1)}\\
   &=\frac{1*\frac{1}{6+1}+REF(EMA(TEMP1),1)*{(1-\frac{1}{6+1})}}{1*\frac{1}{6+1}+REF(EMA(TEMP2),1)*{(1-\frac{1}{6+1})}}\\
   &=\frac{1*\frac{1}{7}+REF(EMA(TEMP1),1)*\frac{6}{7}}{1*\frac{1}{7}+REF(EMA(TEMP2),1)*\frac{6}{7}}\\
   &=\frac{1*\frac{1}{7}+0.5*\frac{6}{7}}{1*\frac{1}{7}+1*\frac{6}{7}}\\
   &=\frac{4}{7}
\end{aligned}$$
</p>

也就是涨了1块后，SMA(TEMP1,6,1)从`1/2`变成了`4/7`,SMA(TEMP2,6,1)仍然是`1`。
我们再假如又跌了1块。那么temp1=0，temp2=1.

<p align="left">
$$\begin{aligned}
RSI&=\frac{SMA(TEMP1,6,1)}{SMA(TEMP2,6,1)}\\
   &=\frac{0*\frac{1}{7}+\frac{4}{7}*\frac{6}{7}}{1*\frac{1}{7}+1*\frac{6}{7}}\\
   &=\frac{24}{49}
\end{aligned}$$
</p>

也就是跌了1块后，SMA(TEMP1,6,1)从`4/7`变成了`24/49`,SMA(TEMP2,6,1)仍然是`1`。

**这里就有意思了，涨了1块，再跌一块，RSI从原来的`50%`变成了`<50%`**
换句话说，如果想让RSI回到原来的`50%`，并不需要跌1块钱，只需要跌不到1块钱。
这其实也就是为什么股价上升期，通过回调RSI，股价还能上去的原因。
反之，跌也是一样的。通过回调RSI，股价还能一路向下。

如果我们考虑极端情况，如果一直涨，分子就会逐渐靠近分母，最后RSI=1。
反之，如果一直跌，分子会逐渐趋向于0，最后RSI=0.


