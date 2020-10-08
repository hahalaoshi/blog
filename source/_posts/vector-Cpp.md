---
title: vector_Cpp
date: 2020-04-22 01:27:35
tags: 
	- C++
	- 数据结构
typora-root-url: ../../source
---

# vector初始化

# 常用接口

### accumulate()

```C++
#include<numeric>
/* 1.累加求和 */
int sum = accumulate(vec.begin() , vec.end() , a);
/* 2.字符串拼接 */
string sum = accumulate(v.begin() , v.end() , string(" "));
```

Tip: 第3行中a是初值