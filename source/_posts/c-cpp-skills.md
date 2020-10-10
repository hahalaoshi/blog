---
title: c/c++刷题技巧
date: 2020-10-11 00:53:36
tags: 
	- leetcode 
	- algorithm
typora-root-url: ../../source
---



https://blog.csdn.net/jiange_zh/article/details/50198097





这篇文章主要记录一些刷leetcode中用的一些刷题技巧。

# C

1. c语言刷题中为什么常常自定义整型的最大值为0x3f3f3f3f？这样做有什么好处？

   首先0x7fffffff不能满足“无穷大加一个有穷的数依然是无穷大”这个条件，它会变成了一个很小的负数。但是0x3f3f3f3f的十进制是1061109567，是10^9级别的（和0x7fffffff一个数量级），而且0x3f3f3f3f+0x3f3f3f3f=2122219134这样可以满足“无穷大加无穷大还是无穷大”的要求。

   重要的一点是，如果用0x3f3f3f3f来表示最大值，那么我们就可以通过memset函数来给数组所有元素赋初值为0x3f3f3f3f。

   

   具体可以参考这篇文章：https://blog.csdn.net/jiange_zh/article/details/50198097

2. 

3. 

# C++