---
title: 内核模块入门1
date: 2020-10-18 20:42:35
tags:
typora-root-url: ../../source
---



这篇文章是linux内核模块系列文章的第一篇，主要讲一下内核模块编译，加载，卸载等基本知识。

# 第一个内核模块

首先，先创建一个内核模块的源码文件，类似下面的代码，提供两个最简单的函数hello_init和hello_exit，这两个函数分别会在内核模块被加载和卸载时被调用。

\__init和__exit这两个宏的作用是把hello_init和hello_exit这两个函数放到指定的段中，这些段一般都是放一些初始化函数和注销函数，初始化和注销函数有一个特点，一般只会被调用1次，把这些函数放在指定的段中，就可以在系统初始化结束后，由于在运行过程中不会再调用这些函数，系统就可以统一回收掉这些函数的内存。

```c
#include <linux/init.h>
#include <linux/kernel.h>
#include <linux/module.h>

static int __init hello_init(void)
{
    printk("Hello linux module\n");
    return 0;
}

static void __exit hello_exit(void)
{
    printk("Good Bye\n");
}

module_init(hello_init);
module_exit(hello_exit);

MODULE_LICENSE("GPL");
```

同时参考下面的代码，写一下Makefile文件:

```shell
obj-m:=hello.o
KERNEL_VERSION:=$(shell uname -r)
KDIR:=/usr/src/linux-headers-$(KERNEL_VERSION)
PWD:=$(shell pwd)
all:
    make -C $(KDIR) M=$(PWD) modules
clean:
    make -C $(KDIR) M=$(PWD) clean
```

在源码目录下执行make命令可以编译生成内核模块

![image-20201018221304012](/images/kernel-module-1/image-20201018221304012.png)



# 内核模块的装载

![image-20201018221526169](/images/kernel-module-1/image-20201018221526169.png)



# linux头文件制作

待补充