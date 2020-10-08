---
title: 数据存储与指针
date: 2020-05-27 21:29:00
tags:
  - armv7
typora-root-url: ../../source
---

# 存储基本概念

## 存储单元

 ![image-20200527213712552](/images/c-memory/image-20200527213712552.png)

思考：一个字一定是占4个字节吗？这是由谁决定的？

不一定，这是由编译器决定的

## 存储模式

### 位序

### 字节序

大端模式，小端模式

 ![image-20200527214256170](/images/c-memory/image-20200527214256170.png)

TODO: 如何通过代码判断当前的处理器模式

 ![image-20200527214727498](/images/c-memory/image-20200527214727498.png)

数据大小端转换

```c
#define swap_endian_u16(A) \ 
((A & 0xFF00 >> 8) | (A & 0x00FF << 8))
```

# 数据溢出

### 表示范围

 ![image-20200527224915121](/images/c-memory/image-20200527224915121.png)

 ![image-20200527225539739](/images/c-memory/image-20200527225539739.png)

### 如何防范

 ![image-20200527225837709](/images/c-memory/image-20200527225837709.png)

# 数据对齐

## 基本概念

 ![image-20200527230414972](/images/c-memory/image-20200527230414972.png)

为什么要数据对齐？

 ![image-20200527230646191](/images/c-memory/image-20200527230646191.png)

## 对齐原则

### 结构体对齐

 ![image-20200527231025265](/images/c-memory/image-20200527231025265.png)

### 联合体对齐

 ![image-20200527231604504](/images/c-memory/image-20200527231604504.png)

示例：

 ![image-20200527231724068](/images/c-memory/image-20200527231724068.png)

 ![image-20200527231902316](/images/c-memory/image-20200527231902316.png)

# 数据类型转换

# 数据的可移植性

## typedef 

```c
typedef unsigned int u16
int main(void)
{
	u16 s;
	return 0;
}
```

根据不同平台的特性，找到一种数据类型，满足长度为16位，然后在typedef中替换

## C99

stdint.h中的int16_t类型



打印不同空间大小的数据类型

 ![image-20200528001618197](/images/c-memory/image-20200528001618197.png)

# size_t类型

 ![image-20200528002556357](/images/c-memory/image-20200528002556357.png)

# 变量的本质

 ![image-20200528005152821](/images/c-memory/image-20200528005152821.png)

## 左值和右值

 ![image-20200528005543796](/images/c-memory/image-20200528005543796.png)

 ![image-20200528005627751](/images/c-memory/image-20200528005627751.png)

# void *

 ![image-20200528012122161](/images/c-memory/image-20200528012122161.png)

 ![image-20200528012158254](/images/c-memory/image-20200528012158254.png)

指针！= 地址

指针变量的值表示地址