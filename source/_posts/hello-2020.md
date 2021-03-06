---
title: hello 2020
tags: 
	- TODO
date: 2020-03-08 12:50:36
typora-root-url: ../../source
---

# 怎样从零构建一个操作系统

操作系统包括kernel和外围包，从这里面应该了解

1. 怎么构建工具链，构建内核，构建rootfs或者initramfs，GRUB和bootloader等
2. 从内核空间到用户空间（1号进程）
3. 操作系统怎么加载进程

参考课程与书籍

- [ ] minios, 写一篇博客
- [ ] 深度探索linux操作系统--系统构建和原理解析，系统学习操作系统构建和加载过程
- [ ] [怎样自制Linux系统？ - 海枫的回答 - 知乎][1] 
- [ ] [linux from scratch][2]
- [ ] 嵌入式课程--使用qemu搭建u-boot-+linux+NFS开发环境，熟悉u-boot，tftp，qemu上arm嵌入式仿真环境
- [ ] [怎么自己实现一个arm-hypervisor(进阶)][3] 
- [ ] [CSP 课堂笔记之 Hypervisor - 高策的文章 - 知乎][4] 

# 操作系统维测能力提升

## gdb使用

[CSAPP---Bomblab][7]

[HITCSAPP大作业][8]

DS-5调试内核

[DS-5 调试Linux内核 - Hello小崔的文章][5]

[ARM FVP (Fixed Virtual Platform) Linux Kernel Debugging Concise][6]

## perf工具

# 操作系统神级课程

## 知名课程

[1]:https://www.zhihu.com/question/272353939/answer/379544629 "怎样自制Linux系统？ - 海枫的回答 - 知乎"
[2]: http://www.linuxfromscratch.org "linux from scratch"
[3]:https://github.com/minos-project/minos-hypervisor "怎么自己实现一个arm-hypervisor"
[4]:https://zhuanlan.zhihu.com/p/34320333 "CSP 课堂笔记之 Hypervisor - 高策的文章 - 知乎"
[5]:https://zhuanlan.zhihu.com/p/81718103 "DS-5 调试Linux内核 - Hello小崔的文章"
[6]:https://www.jianshu.com/p/c0a9a4b9569d "ARM FVP (Fixed Virtual Platform) Linux Kernel Debugging Concise"
[7]:https://zhuanlan.zhihu.com/p/104130161 "CSAPP---Bomblab"
[8]:https://zhuanlan.zhihu.com/p/99176009 "HITCSAPP大作业"

## 嵌入式linux高级课程

第二期

- [x] 2.1 计算机体系结构
- [x] 2.2 X86与ARM架构对比分析
- [x] 2.3 C51与ARM架构对比分析
- [x] 2.4 总线与地址
- [ ] 2.5 指令集、微架构与编译器
- [ ] 2.6 ARM体系结构与寻址方式
- [ ] 2.7 ARM汇编指令
- [ ] 2.8 ARM伪指令
- [ ] 2.9 ARM汇编程序与伪操作
- [ ] 2.10 C和汇编混合编程
- [ ] 2.11 GNU ARM汇编语言
- [ ] 2.12 链接脚本
- [ ] 2.13 嵌入式系统启动流程

第三期

- [x] 3.1 程序的编译与可执行文件
- [x] 3.2 GCC命令参数
- [x] 3.3 预处理过程
- [x] 3.4 编译过程(1)：从源程序到汇编文件
- [x] 3.5 编译过程(2)：汇编过程
- [x] 3.6 编译过程(3)：符号表
- [x] 3.7 链接过程(1)：地址空间分配与链接脚本
- [x] 3.8 链接过程(2)：符号解析-强符号与弱符号
- [x] 3.9 链接过程(3)：重定位
- [x] 3.10 程序的运行
- [x] 3.11 BSS段的处理
- [ ] 3.12 main函数入口分析
- [x] 3.13 链接静态库
- [ ] 3.14 动态链接(1)：与位置无关的代码
- [ ] 3.15 动态链接(2)：全局符号表GOT
- [ ] 3.16 动态链接(3)：共享库
- [x] 3.17 开发一个插件
- [x] 3.18 内核模块加载机制
- [x] 3.19 binutils工具集
- [ ] 3.20 linux内核加载实验
- [ ] 3.21 u-boot重定位分析(上)
- [ ] 3.22 u-boot重定位分析(下)

第四期

- [x] 4.1 程序与内存的关系
- [x] 4.2 栈的初始化及大小
- [ ] 4.3 栈的管理：函数调用
- [ ] 4.4 栈的管理：参数传递
- [ ] 4.5 形参与实参
- [ ] 4.6 栈与作用域
- [ ] 4.7 栈溢出攻击原理
- [ ] 4.8 实战：栈溢出攻击示例
- [ ] 4.9 堆内存管理：内存申请与释放
- [ ] 4.10 ucos堆内存管理
- [ ] 4.11 Linux堆内存管理(1)：内存分配器
- [ ] 4.12 linux堆内存管理(2)：内存申请与释放
- [ ] 4.13 Linux堆内存管理(3)：内存申请释放示例
- [ ] 4.14 内存泄露与防范
- [ ] 4.15 常见内存错误及检测
- [ ] 4.16 实战&作业：实现自己的堆管理器

第五期

- [ ] 5.1 什么是C语言标准？
- [ ] 5.2 C标准发展过程及新增特性
- [ ] 5.3 语句表达式
- [ ] 5.4 typeof
- [ ] 5.5 container_of
- [ ] 5.6 零长度数组
- [x] 5.7 属性声明：section
- [x] 5.8 属性声明：aligned & packed
- [ ] 5.9 属性声明：format
- [x] 5.10 属性声明：const
- [ ] 5.11 属性声明：weak & alias
- [ ] 5.12 属性声明：constructor & destructor
- [ ] 5.13 属性声明：noinline & always_inline
- [ ] 5.14 属性声明：mode
- [ ] 5.15 属性声明：noreturn
- [ ] 5.16 属性声明：used & unused
- [x] 5.17 内建函数
- [ ] 5.18 内建函数：__builtin_constant_p
- [x] 5.19 内建函数：__builtin_expect
- [ ] 5.20 case范围扩展
- [ ] 5.21 do{}while(0)
- [ ] 5.22 可变参数宏
- [ ] 5.23 局部标签
- [ ] 5.24 标号元素

第六期

- [ ] 6.1 存储才是C语言的精髓
- [ ] 6.2 存储的基本概念
- [ ] 6.3 有符号数和无符号数
- [ ] 6.4 数据溢出
- [ ] 6.5 数据对齐
- [ ] 6.6 数据类型转换
- [ ] 6.7 数据的可移植性
- [ ] 6.8 内核中的size_t数据类型
- [ ] 6.9 变量的本质
- [ ] 6.10 常量存储
- [ ] 6.11 从变量到指针
- [ ] 6.12 一些复杂的指针声明
- [ ] 6.13 指针类型与运算
- [ ] 6.14 指针与数组的暧昧：下标运算
- [ ] 6.15 指针与数组的暧昧：数组名
- [ ] 6.16 指针与数组的暧昧：数值指针与指针数组
- [ ] 6.17 指针与结构体
- [ ] 6.18 二级指针：修改指针变量
- [ ] 6.19 二级指针：指针数组传参
- [ ] 6.20 二级指针：二维数组
- [ ] 6.21 指针函数与函数指针
- [ ] 6.22 重新认识void

第九期

- [x] 9.1 CPU和操作系统入门
- [x] 9.2 多任务的裸机实现(上)
- [x] 9.3 多任务的裸机实现(下)
- [x] 9.4 调度器的工作原理
- [ ] 9.5 函数栈与进程栈
- [ ] 9.6 可重入函数
- [ ] 9.7 临界区与临界资源
- [ ] 9.8 系统调用(上)
- [ ] 9.9 系统调用(下)
- [ ] 9.10 中断(上)：中断处理流程
- [ ] 9.11 中断(中)：进程栈与中断栈
- [ ] 9.12 中断(下)：中断函数的编写
- [ ] 9.13 存储器映射(上)
- [ ] 9.14 存储器映射(下)
- [ ] 9.15 存储抽象：文件系统
- [ ] 9.16 内存、外存与外设
- [ ] 9.17 IO端口与IO内存
- [ ] 9.18 位运算(上)
- [ ] 9.19 位运算(下)
- [ ] 9.20 内存管理单元MMU(上)
- [ ] 9.21 内存管理单元MMU(下)
- [ ] 9.23 本期小结