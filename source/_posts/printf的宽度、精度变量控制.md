---
title: printf的宽度、精度变量控制
categories: acm
date: 2020-10-04 02:26:56
tags:
---

算法竞赛中有一题要求保留小数点后`x`位，`x`由用户输入。
这里需要用到printf的精度变量控制。
<!--more-->

printf format中可以用`*`来表示变量。

> 示例

### 小数点精度
`input`
````cpp
1 6 4
1 0 0
````
`code`
````cpp
while (3 == scanf("%d %d %d", &a, &b, &c) && a) {
    printf("%.*lf",c,(double)a/b);
}
````
`terminal`
````shell
0.1667
````
<br>

### 字符串的宽度+精度
`code`
````cpp
string s="abc123";
int a=10,b=3;

printf("%*.*s\n", a,b, s.c_str());
printf("%-*.*s\n", a,b, s.c_str());
````
`terminal`
````shell
       abc
abc
````
<br>




