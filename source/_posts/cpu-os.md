---
title: CPU和操作系统入门
date: 2020-05-10 22:59:30
tags:
  - cpu
typora-root-url: ../../source
---

# 多任务的裸机实现

## 简单模拟

嵌入式上的任务运行：

```shell
+----------------------+
|                      |
|                      |
|                      v
|       +--------------+-------------+
|       |                            |
|       |    task_get_temperature    |
|       |                            |
|       +--------------+-------------+
|                      |
|                      |
|                      v
|       +--------------+-------------+
|       |                            |
|       |        task_led_show       |
|       |                            |
|       +--------------+-------------+
|                      |
|                      |
|                      v
|       +--------------+-------------+
|       |                            |
|       |        task_key_scan       |
|       |                            |
|       +--------------+-------------+
|                      |
|                      |
|                      v
|       +--------------+-------------+
|       |                            |
|       |    task_set_temperature    |
|       |                            |
|       +--------------+-------------+
|                      |
|                      |
+----------------------+
```



# 调度器的工作原理

## 基本定义

 ![image-20200510233908789](/images/cpu-os/image-20200510233908789.png)

## 调度器类型

| 类型       | 属性               |
| ---------- | ------------------ |
| 不可抢占型 | 时间片轮转         |
| 可抢占     | 高优先级任务先运行 |

## 实例

用signal和alarm模拟中断，进行任务调度

源码路径：[os_schedule.c][1]

```shell
root@xmalloc:~/blog-src/cpu-os/4# gcc os_schedule.c
os_schedule.c: In function ‘os_init’:
os_schedule.c:46:21: warning: passing argument 2 of ‘signal’ from incompatible pointer type [-Wincompatible-pointer-types]
     signal(SIGALRM, timer_interrupt);
                     ^~~~~~~~~~~~~~~
In file included from os_schedule.c:3:0:
/usr/include/signal.h:88:23: note: expected ‘__sighandler_t {aka void (*)(int)}’ but argument is of type ‘void (*)(void)’
 extern __sighandler_t signal (int __sig, __sighandler_t __handler)
                       ^~~~~~
root@xmalloc:~/blog-src/cpu-os/4# ./a.out
Task 4...
Task 4...
Task 4...
Task 4...
Task 2...
Task 3...
Task 4...
Task 4...
Task 4...
Task 2...
Task 3...
Task 4...
Task 4...
Task 4...
Task 1...
Task 4...
Task 2...
Task 3...
Task 4...
```



# 函数栈与进程栈

在任务调度过程中，每当发生任务切换，系统应该保留任务切换前的现场信息，然后再将任务切出，在任务切回来之后，应该恢复任务的现场信息，让任务对于切换这个过程不感知。

[1]: https://github.com/hahalaoshi/blog-src/blob/master/cpu-os/4/os_schedule.c