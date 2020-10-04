---
title: scanf
categories: acm
date: 2020-10-04 01:27:43
tags:
---

关于scanf的返回值。
<!--more-->

scanf返回成功赋值的个数。
如果1个都没有，返回的是-1（注意不是0）。
`while (scanf(xxx) == n)`一定要判断，因为空行返回的是-1，会死循环的！

> 示例

`input`
````cpp
2 1 6
2 1 3
3 4
````

`code`
````cpp
ret = scanf("%d %d %d", &a, &b, &c);
printf("ret:%d, %d, %d, %d\n", ret,a,b,c);
ret = scanf("%d %d %d", &a, &b, &c);
printf("ret:%d, %d, %d, %d\n", ret,a,b,c);
ret = scanf("%d %d %d", &a, &b, &c);
printf("ret:%d, %d, %d, %d\n", ret,a,b,c);
ret = scanf("%d %d %d", &a, &b, &c);
printf("ret:%d, %d, %d, %d\n", ret,a,b,c)
````

`terminal`
````shell
ret:3, 2, 1, 6
ret:3, 2, 1, 3
ret:2, 3, 4, 3
ret:-1, 3, 4, 3
````


