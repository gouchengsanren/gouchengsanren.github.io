---
title: hexo中使用MathJax
categories: hexo
date: 2020-10-08 09:01:52
mathjax: true
tags:
---

一些交易指标的公式编写，需要用到MathJax引擎。
记录hexo中如何使用它。
<!--more-->
<br>

直接插入js脚本是不行的。
比如：
````c
<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>
````
本地 `hexo s` 运行良好，但 `hexo d` 部署上去，是无法显示公式的。


其实hexo的主题，比如我在用的next是自带mathjax支持的，只需要使能它。

使能MathJax：
`./themes/next/_config.yml`
````c
505   mathjax:
506     enable: true
````

在post头添加一行：
`mathjax: true`
即：
````c
---
title: hexo中使用MathJax
categories: hexo
date: 2020-10-08 09:01:52
mathjax: true
tags:
---
````


公式目前测试只能用`$$ $$`包裹。不支持`$ $`包裹。前者是块，后者是行。
而且还需要在外面套一个`<p>`标签。原因不明。试出来的。

> 举例
````c
<p align="left">
$$\begin{aligned}
\sum_{i=0}^N\int_{a}^{b}g(t,i)\text{d}t
\end{aligned}$$
</p>
````

> 效果
<p align="left">
$$\begin{aligned}
\sum_{i=0}^N\int_{a}^{b}g(t,i)\text{d}t
\end{aligned}$$
</p>

其中 `\begin{aligned}` 是为了多行公式可以对齐。
默认是右对齐。

比如：
<p align="left">
$$\begin{aligned}
a=x_1+x_2+x_3\\
b=x_4+x_5\\
c=x_6
\end{aligned}$$
</p>

对齐后：
<p align="left">
$$\begin{aligned}
a&=x_1+x_2+x_3\\
b&=x_4+x_5\\
c&=x_6
\end{aligned}$$
</p>




