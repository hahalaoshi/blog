---
title: 程序的编译、链接和运行
date: 2020-05-05 17:11:34
tags:
  - 编译
  - 链接
typora-root-url: ../../source
---

# 程序的编译与可执行文件

## ELF可执行文件结构

```c
+----------------------+
|      ELF header      |---->描述文件类型、要运行的处理器平台等信息
+----------------------+
| program header table |---->描述有哪些段(segment),段通常由几个section组成,段是给加载器使用的
+----------------------+
|         .init        |
+----------------------+
|         .text        |---->可执行指令代码
+----------------------+
|        .rodata       |---->字符串、常量及printf打印的字符串常量等
+----------------------+
|         .data        |---->初始化的全局变量和静态局部变量
+----------------------+
|         .bss         |---->未初始化的全局变量
+----------------------+
|        .symtab       |---->符号表，存放符号的地址等
+----------------------+
|        .debug        |
+----------------------+
|        .line         |
+----------------------+
|       .strtab        |---->字符串表，存放函数名、变量名，用于调试、反汇编
+----------------------+
| section header table |---->描述有哪些section,section是给链接器使用的
+----------------------+
```

## 目标文件的生成

下面通过一个例子来展示不同目标文件的编译过程。

源码路径：[main.c][1]  [sub.c][2]

```shell
# gcc的-c选项表示不执行链接
gcc -c main.c sub.c
```

执行上面的命令会生成 `main.o` 和 `sub.o` 这两个可重定位目标文件(relocatable files)

```shell
gcc main.o sub.o
```

执行上面的命令会生成 `a.out` 可执行目标文件(executable files)

```shell
ar rcs libmath.a sub.o
```

执行上面的命令会生成 `libmath.a` 的目标文件，这个本质上还是可重定位目标文件，`.a` 的目标文件只是一些可重定位目标文件的归档

```shell
gcc -shared -fPIC -o libmath.so sub.c
```

执行上面的命令会生成 `libmath.so` 的可被共享的目标文件(shared object files)，一般被称作动态链接库

# GCC常用命令参数

下面的表格总结了一些常用的编译命令选项

| 参数      | 说明                                                         |
| --------- | ------------------------------------------------------------ |
| -E        | 预处理，但不生成文件                                         |
| -S        | 只激活预处理和编译，生成汇编代码                             |
| -c        | 将源文件编译成目标文件，但是不链接                           |
| -o        | 指定输出的目标文件名称                                       |
| -M        | 生成文件关联依赖关系                                         |
| -MD       | 和-M相同，但是会将输出导入到.d文件里面                       |
| -MM       | 生成文件依赖关系，但忽略#include<file.h>产生的依赖关系(**注：在编写Makefile非常有用**) |
| -g        | 编译时产生调试信息                                           |
| -static   | 禁止使用动态库                                               |
| -share    | 尽量使用动态库                                               |
| -Ldir     | 指定库搜索路径                                               |
| -llibname | 指定编译时使用的库。 gcc -lpthread main.c                    |
| -Idir     | 指定头文件搜索路径                                           |
| -shared   | 生成共享目标文件                                             |
| -w        | 不生成任何警告信息                                           |
| -Wall     | 生成所有警告信息                                             |
| -std=C99  | 指定c标准，gcc默认的标准是GNU C                              |

常用命令演示：

```shell
# 预处理
root@xmalloc:~/blog-src/compile-basic/1# gcc -E main.c > main.i
# 生成汇编文件
root@xmalloc:~/blog-src/compile-basic/1# gcc -S main.c
# 显示gcc工作过程中的日志信息
root@xmalloc:~/blog-src/compile-basic/1# gcc main.c sub.c --verbose
# 显示文件关联依赖关系
root@xmalloc:~/blog-src/compile-basic/1# gcc -M main.c
main.o: main.c /usr/include/stdc-predef.h /usr/include/stdio.h \
 /usr/include/x86_64-linux-gnu/bits/libc-header-start.h \
 /usr/include/features.h /usr/include/x86_64-linux-gnu/sys/cdefs.h \
 /usr/include/x86_64-linux-gnu/bits/wordsize.h \
 /usr/include/x86_64-linux-gnu/bits/long-double.h \
 /usr/include/x86_64-linux-gnu/gnu/stubs.h \
 /usr/include/x86_64-linux-gnu/gnu/stubs-64.h \
 /usr/lib/gcc/x86_64-linux-gnu/7/include/stddef.h \
 /usr/include/x86_64-linux-gnu/bits/types.h \
 /usr/include/x86_64-linux-gnu/bits/typesizes.h \
 /usr/include/x86_64-linux-gnu/bits/types/__FILE.h \
 /usr/include/x86_64-linux-gnu/bits/types/FILE.h \
 /usr/include/x86_64-linux-gnu/bits/libio.h \
 /usr/include/x86_64-linux-gnu/bits/_G_config.h \
 /usr/include/x86_64-linux-gnu/bits/types/__mbstate_t.h \
 /usr/lib/gcc/x86_64-linux-gnu/7/include/stdarg.h \
 /usr/include/x86_64-linux-gnu/bits/stdio_lim.h \
 /usr/include/x86_64-linux-gnu/bits/sys_errlist.h
# 生成依赖关系，忽略#include<file.h>产生的依赖关系
root@xmalloc:~/blog-src/compile-basic/1# gcc -MM main.c
main.o: main.c
# 指定链接库文件
root@xmalloc:~/blog-src/compile-basic/1# ar rcs libmath.a sub.o
root@xmalloc:~/blog-src/compile-basic/1# gcc main.c -L. -lmath -o lib_app
root@xmalloc:~/blog-src/compile-basic/1# ./lib_app
a = 5
b = 1
```



# 预处理过程

预处理主要完成下面两项工作：

1. 宏命令展开
2. 文本替换

## #pragma宏

一些常见用法：

| 宏定义                     | 说明                                                 |
| -------------------------- | ---------------------------------------------------- |
| \#pragma pack([n])         | 指示结构体和联合成员的对齐方式，例：\#pragma pack(4) |
| \#pragma message("string") | 编译信息输出窗口打印文本信息                         |
| \#pragma warning           | 有选择的改变编译器的警告信息行为                     |
| \#pragma once              | 防止头文件多次编译                                   |
| \#error "error meggage"    | 制造编译错误，人为的停止编译                         |

应用：

**TODO：优雅地展开宏**



# 编译过程

**基本过程**：

- 词法分析
- 语法分析
- 语义分析
- 中间代码生成
- 汇编代码生成
- 目标代码生成

**现代编译器的构造**：

- 前端：词法分析、语法分析、语义分析
- 优化器：对中间代码进行优化
- 后端：指令选择、寄存器分配

## 源文件到汇编文件

输入：C程序源文件

输出：汇编文件、目标文件

**生成中间代码的好处**：

![Screen Shot 2020-05-05 at 8.39.55 PM](/images/compile-basic/Screen%20Shot%202020-05-05%20at%208.39.55%20PM.png)



## 汇编过程

**主要工作：**

1. 指令翻译
2. 生成各种表信息(为后面链接提供信息)

![Screen Shot 2020-05-05 at 8.59.20 PM](/images/compile-basic/Screen%20Shot%202020-05-05%20at%208.59.20%20PM.png)



### 查看符号表

```shell
root@xmalloc:~/blog-src/compile-basic/1# gcc -c sub.c
root@xmalloc:~/blog-src/compile-basic/1# ls
lib_app  libmath.a  main.c  main.o  main.s  sub.c  sub.o
root@xmalloc:~/blog-src/compile-basic/1# readelf -s sub.o

Symbol table '.symtab' contains 12 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND
     1: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS sub.c
     2: 0000000000000000     0 SECTION LOCAL  DEFAULT    1
     3: 0000000000000000     0 SECTION LOCAL  DEFAULT    2
     4: 0000000000000000     0 SECTION LOCAL  DEFAULT    3
     5: 0000000000000000     0 SECTION LOCAL  DEFAULT    5
     6: 0000000000000000     0 SECTION LOCAL  DEFAULT    6
     7: 0000000000000000     0 SECTION LOCAL  DEFAULT    4
     8: 0000000000000000    20 FUNC    GLOBAL DEFAULT    1 add
     9: 0000000000000014    18 FUNC    GLOBAL DEFAULT    1 sub
    10: 0000000000000026    19 FUNC    GLOBAL DEFAULT    1 mul
    11: 0000000000000039    19 FUNC    GLOBAL DEFAULT    1 div
```

首先执行第1行的命令生成目标文件 `sub.o` , 然后执行第4行命令查看符号表信息，可以看到第16-19行显示了这几个函数相对于零地址的一个偏移。

```shell
root@xmalloc:~/blog-src/compile-basic/1# gcc -c main.c
root@xmalloc:~/blog-src/compile-basic/1# ls
lib_app  libmath.a  main.c  main.o  main.s  sub.c  sub.o
root@xmalloc:~/blog-src/compile-basic/1# readelf -s main.o

Symbol table '.symtab' contains 18 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND
     1: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS main.c
     2: 0000000000000000     0 SECTION LOCAL  DEFAULT    1
     3: 0000000000000000     0 SECTION LOCAL  DEFAULT    3
     4: 0000000000000000     0 SECTION LOCAL  DEFAULT    4
     5: 0000000000000000     0 SECTION LOCAL  DEFAULT    5
     6: 0000000000000000     4 OBJECT  LOCAL  DEFAULT    4 uninit_local_val.2261
     7: 0000000000000004     4 OBJECT  LOCAL  DEFAULT    3 local_val.2260
     8: 0000000000000000     0 SECTION LOCAL  DEFAULT    7
     9: 0000000000000000     0 SECTION LOCAL  DEFAULT    8
    10: 0000000000000000     0 SECTION LOCAL  DEFAULT    6
    11: 0000000000000000     4 OBJECT  GLOBAL DEFAULT    3 global_val
    12: 0000000000000004     4 OBJECT  GLOBAL DEFAULT  COM uinit_val
    13: 0000000000000000    95 FUNC    GLOBAL DEFAULT    1 main
    14: 0000000000000000     0 NOTYPE  GLOBAL DEFAULT  UND _GLOBAL_OFFSET_TABLE_
    15: 0000000000000000     0 NOTYPE  GLOBAL DEFAULT  UND add
    16: 0000000000000000     0 NOTYPE  GLOBAL DEFAULT  UND sub
    17: 0000000000000000     0 NOTYPE  GLOBAL DEFAULT  UND printf
```

通过类似的方法可以查看 `main.o` 的符号表，不同的是第23-25行显示的3个函数是未定义的，虽然 `main.c` 中未对这几个函数进行定义，但是在编译过程中也没有报错，只有在后续链接过程中，依然找不到这几个函数的定义，链接器才会报符号未定义的错误。



### 查看重定位表

```shell
root@xmalloc:~/blog-src/compile-basic/1# readelf -r main.o

Relocation section '.rela.text' at offset 0x340 contains 6 entries:
  Offset          Info           Type           Sym. Value    Sym. Name + Addend
000000000013  000f00000004 R_X86_64_PLT32    0000000000000000 add - 4
000000000025  001000000004 R_X86_64_PLT32    0000000000000000 sub - 4
000000000034  000500000002 R_X86_64_PC32     0000000000000000 .rodata - 4
00000000003e  001100000004 R_X86_64_PLT32    0000000000000000 printf - 4
00000000004a  000500000002 R_X86_64_PC32     0000000000000000 .rodata + 4
000000000054  001100000004 R_X86_64_PLT32    0000000000000000 printf - 4

Relocation section '.rela.eh_frame' at offset 0x3d0 contains 1 entry:
  Offset          Info           Type           Sym. Value    Sym. Name + Addend
000000000020  000200000002 R_X86_64_PC32     0000000000000000 .text + 0
```

在`readelf` 命令中加 `-r` 选项，就可以看目标文件的重定位表，可以看到在 `main.o` 中哪些函数需要重定位。



## 符号表

### 符号表信息

- 编译过程中，符号表用来保存源程序中各种符号的信息
- 主要包括符号的地址值、类型、占用空间的大小



#### 符号值(Value)

本质是一个地址，在可重定位目标文件中表示相对地址，在可执行目标文件中表示绝对地址：

```shell
root@xmalloc:~/blog-src/compile-basic/1# readelf -s sub.o

Symbol table '.symtab' contains 12 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND
     1: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS sub.c
     2: 0000000000000000     0 SECTION LOCAL  DEFAULT    1
     3: 0000000000000000     0 SECTION LOCAL  DEFAULT    2
     4: 0000000000000000     0 SECTION LOCAL  DEFAULT    3
     5: 0000000000000000     0 SECTION LOCAL  DEFAULT    5
     6: 0000000000000000     0 SECTION LOCAL  DEFAULT    6
     7: 0000000000000000     0 SECTION LOCAL  DEFAULT    4
     8: 0000000000000000    20 FUNC    GLOBAL DEFAULT    1 add
     9: 0000000000000014    18 FUNC    GLOBAL DEFAULT    1 sub
    10: 0000000000000026    19 FUNC    GLOBAL DEFAULT    1 mul
    11: 0000000000000039    19 FUNC    GLOBAL DEFAULT    1 div
root@xmalloc:~/blog-src/compile-basic/1# readelf -s a.out

Symbol table '.symtab' contains 72 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
    39: 0000000000000994     0 OBJECT  LOCAL  DEFAULT   18 __FRAME_END__
    40: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS
    41: 0000000000200dc0     0 NOTYPE  LOCAL  DEFAULT   19 __init_array_end
    42: 0000000000200dc8     0 OBJECT  LOCAL  DEFAULT   21 _DYNAMIC
    43: 0000000000200db8     0 NOTYPE  LOCAL  DEFAULT   19 __init_array_start
    44: 00000000000007b4     0 NOTYPE  LOCAL  DEFAULT   17 __GNU_EH_FRAME_HDR
    45: 0000000000200fb8     0 OBJECT  LOCAL  DEFAULT   22 _GLOBAL_OFFSET_TABLE_
    46: 0000000000000790     2 FUNC    GLOBAL DEFAULT   14 __libc_csu_fini
    47: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_deregisterTMCloneTab
    48: 0000000000201000     0 NOTYPE  WEAK   DEFAULT   23 data_start
    49: 00000000000006c9    20 FUNC    GLOBAL DEFAULT   14 add
    50: 0000000000201018     0 NOTYPE  GLOBAL DEFAULT   23 _edata
    51: 0000000000000794     0 FUNC    GLOBAL DEFAULT   15 _fini
    52: 0000000000000702    19 FUNC    GLOBAL DEFAULT   14 div
    53: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND printf@@GLIBC_2.2.5
    54: 0000000000201010     4 OBJECT  GLOBAL DEFAULT   23 global_val
    55: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND __libc_start_main@@GLIBC_
    56: 0000000000201000     0 NOTYPE  GLOBAL DEFAULT   23 __data_start
    57: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
    58: 0000000000201008     0 OBJECT  GLOBAL HIDDEN    23 __dso_handle
    59: 00000000000007a0     4 OBJECT  GLOBAL DEFAULT   16 _IO_stdin_used
    60: 0000000000000720   101 FUNC    GLOBAL DEFAULT   14 __libc_csu_init
    61: 0000000000201028     0 NOTYPE  GLOBAL DEFAULT   24 _end
    62: 0000000000000560    43 FUNC    GLOBAL DEFAULT   14 _start
    63: 0000000000201018     0 NOTYPE  GLOBAL DEFAULT   24 __bss_start
    64: 000000000000066a    95 FUNC    GLOBAL DEFAULT   14 main
    65: 00000000000006ef    19 FUNC    GLOBAL DEFAULT   14 mul
    66: 0000000000201018     0 OBJECT  GLOBAL HIDDEN    23 __TMC_END__
    67: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_registerTMCloneTable
    68: 00000000000006dd    18 FUNC    GLOBAL DEFAULT   14 sub
    69: 0000000000000000     0 FUNC    WEAK   DEFAULT  UND __cxa_finalize@@GLIBC_2.2
    70: 0000000000000510     0 FUNC    GLOBAL DEFAULT   11 _init
    71: 0000000000201020     4 OBJECT  GLOBAL DEFAULT   24 uinit_val
```

可以看到，对不同类型的目标文件，查看其符号值，同一个符号，其值是不同的。



#### 符号类型(Type)

| 类型    | 含义                                       |
| ------- | ------------------------------------------ |
| OBJECT  | 符号关联的是一个数据对象: 变量、数组或指针 |
| FUNC    | 符号关联到一个函数或者过程                 |
| SECTION | 符号关联到一个节的名字                     |
| FILE    | 符号关联一个文件名                         |
| NOTYPE  | 符号的类型未指定，其用于未定义引用         |



#### 绑定属性(Bind)

| 类型   | 含义                                                         |
| ------ | ------------------------------------------------------------ |
| LOCAL  | 局部符号，目标文件内可见。多文件重名不冲突                   |
| GLOBAL | 全局符号，在目标文件内部可见，也可以被其他文件引用           |
| WEAK   | 弱符号，整个程序可见，多文件可重复定义。即使若符号未定义，链接也不报错，将符号值设置为0 |



#### 节索引(Ndx)

表示在Section头文件表中对应section的索引，可以通过`readelf -S` 查看section头文件表。

Ndx是非数值表示的特殊情况：

| 类型   | 含义                                     |
| ------ | ---------------------------------------- |
| ABS    | 指定符号的绝对值，不需要重定位的符号     |
| UNDEF  | 未定义符号，本模块引用，但在其他地方定义 |
| COMMON | 标识还未分配位置的未初始化的数据         |



### 符号表作用

- 辅助语义检查：看源程序是否有语义错误
- 辅助代码生成：地址与空间分配、符号决议、重定位



### ELF文件和BIN文件差异

文件结构：

1. BIN文件只包含机器码，纯粹的程序文件
2. ELF文件除机器码外，还有一些额外的信息：段的加载地址、运行地址、符号表、重定位表等

运行方式：

1. BIN文件运行，只需要加载到链接地址即可
2. ELF文件运行，需要OS环境和加载器(loader、ld-linux.so)



### 总结

- 为后续的链接、运行过程提供必要的信息
- 链接器根据重定位表、符号表进行链接、重定位后续操作，生成可以运行的可执行文件
- 加载器根据文件头、程序头信息，将程序加载到指定内存运行



# 链接过程

## 基本定义

**目的：**将所有的可重定位目标文件合并、组装成可执行目标文件

**主要步骤：** 

- 地址空间分配
- 符号解析：强符号与弱符号
- 重定位



## 地址空间分配与链接脚本

### 地址空间分配

**步骤：**

1. 扫描所有目标文件，从各个文件段表中获取各个文件代码段、数据段信息：大小、地址等
2. 从指定的链接地址开始，合并各个目标文件同类型的段，并重新计算各个段的偏移和位置
3. 收集各目标文件符号表中的符号，统一放到全局符号表中（注：此时全局符号表中的地址还是相对零地址的偏移）

![Screen Shot 2020-05-05 at 10.43.56 PM](/images/compile-basic/Screen%20Shot%202020-05-05%20at%2010.43.56%20PM-8691689.png)



### 链接脚本

**作用：**

- 规定了各个段的组装顺序、起始地址、位置对齐等
- 规定输出可执行文件的格式、运行平台、入口地址等信息
- 链接器根据链接脚本进行组装

**示例：**

```
#include <config/config.h>

// 输出elf文件格式
OUTPUT_FORMAT("elf32-littlearm")
// 输出可执行文件的运行平台为arm
OUTPUT_ARCH("arm")
// 程序入口地址
ENTRY(_start)
// 各段描述
SECTIONS
{
	.vectors CONFIG_MINOS_ENTRY_ADDRESS:
	{
		/*
		 * put all asm code into this section
		 */
		__code_start = .;
		KEEP(*(__start_up))
		KEEP(*(__el2_vectors __int_handlers __asm_code))
	}

	//代码段起始地址
	. = 0x60000000;
	.text :
	{
	  // 代码段描述:所有.o文件的.text
		*(.text)
	}
	. = 0x60200000;
  // 数据段描述
	.data : {*(.data)}
}
```

**查看链接加载地址：**

```shell
root@xmalloc:~/blog-src/compile-basic/1# readelf -l a.out

Elf file type is DYN (Shared object file)
Entry point 0x560
There are 9 program headers, starting at offset 64

Program Headers:
  Type           Offset             VirtAddr           PhysAddr
                 FileSiz            MemSiz              Flags  Align
  PHDR           0x0000000000000040 0x0000000000000040 0x0000000000000040
                 0x00000000000001f8 0x00000000000001f8  R      0x8
  INTERP         0x0000000000000238 0x0000000000000238 0x0000000000000238
                 0x000000000000001c 0x000000000000001c  R      0x1
      [Requesting program interpreter: /lib64/ld-linux-x86-64.so.2]
  LOAD           0x0000000000000000 0x0000000000000000 0x0000000000000000
                 0x0000000000000998 0x0000000000000998  R E    0x200000
  LOAD           0x0000000000000db8 0x0000000000200db8 0x0000000000200db8
                 0x0000000000000260 0x0000000000000270  RW     0x200000
  DYNAMIC        0x0000000000000dc8 0x0000000000200dc8 0x0000000000200dc8
                 0x00000000000001f0 0x00000000000001f0  RW     0x8
  NOTE           0x0000000000000254 0x0000000000000254 0x0000000000000254
                 0x0000000000000044 0x0000000000000044  R      0x4
  GNU_EH_FRAME   0x00000000000007b4 0x00000000000007b4 0x00000000000007b4
                 0x000000000000005c 0x000000000000005c  R      0x4
  GNU_STACK      0x0000000000000000 0x0000000000000000 0x0000000000000000
                 0x0000000000000000 0x0000000000000000  RW     0x10
  GNU_RELRO      0x0000000000000db8 0x0000000000200db8 0x0000000000200db8
                 0x0000000000000248 0x0000000000000248  R      0x1

 Section to Segment mapping:
  Segment Sections...
   00
   01     .interp
   02     .interp .note.ABI-tag .note.gnu.build-id .gnu.hash .dynsym .dynstr .gnu.version .gnu.version_r .rela.dyn .rela.plt .init .plt .plt.got .text .fini .rodata .eh_frame_hdr .eh_frame
   03     .init_array .fini_array .dynamic .got .data .bss
   04     .dynamic
   05     .note.ABI-tag .note.gnu.build-id
   06     .eh_frame_hdr
   07
   08     .init_array .fini_array .dynamic .got
```

第15行显示了可执行文件的加载地址



## 符号解析

**问题：**

​	一个项目由多个模块、文件构成，多人共同开发，不同模块、文件中的变量或者函数可能存在重名的情况

**强/弱符号：**

| 类型   | 定义               |
| ------ | ------------------ |
| 强符号 | 函数名             |
|        | 初始化的全局变量   |
| 弱符号 | 未初始化的全局变量 |

**编译器符号处理规则：**

| 类型   | 规则                                                         |
| ------ | ------------------------------------------------------------ |
| 强符号 | 不允许多次定义，但强弱可以共存                               |
|        | 强弱共存时，强覆盖弱，链接时会选择强符号                     |
| 弱符号 | 允许多个弱符号                                               |
|        | 编译期间，编译器并不知道弱符所占空间大小，使用COMMON符号来代替 |
|        | 链接期间，链接器比较多个弱符号，选择占用空间最大的           |

**弱符号与BSS段：**

 ![Screen Shot 2020-05-05 at 11.57.52 PM](/images/compile-basic/Screen%20Shot%202020-05-05%20at%2011.57.52%20PM.png)

**强符号转弱符号：**

\__attribute__((weak))

```shell
__attribute__((weak)) int n = 100;
__attribute__((weak)) void fun();
```

**弱引用的应用**

 ![Screen Shot 2020-05-06 at 1.20.41 AM](/images/compile-basic/Screen%20Shot%202020-05-06%20at%201.20.41%20AM.png)



源码路径： [main.c][3] [libdecode.c][4] [decode.h][5]

例1：用自定义函数覆盖库中函数

```shell
root@xmalloc:~/blog-src/compile-basic/2# gcc main.c libdecode.c
root@xmalloc:~/blog-src/compile-basic/2# ls
a.out  decode.h  libdecode.c  main.c
root@xmalloc:~/blog-src/compile-basic/2# ./a.out
lib:decode()
return...
```

修改 `main.c` ,添加下面的函数定义后重新编译运行：

```c
void decode()
{
    printf("my:decode()\n");
}
```

```shell
root@xmalloc:~/blog-src/compile-basic/2# gcc main.c libdecode.c
root@xmalloc:~/blog-src/compile-basic/2# ls
a.out  decode.h  libdecode.c  main.c
root@xmalloc:~/blog-src/compile-basic/2# ./a.out
my:decode()
return...
root@xmalloc:~/blog-src/compile-basic/2#
```

例2：当删除某些模块或者不编译库文件时，程序也能正常链接、运行

```shell
root@xmalloc:~/blog-src/compile-basic/2# gcc main.c
root@xmalloc:~/blog-src/compile-basic/2# ls
a.out  decode.h  libdecode.c  main.c
root@xmalloc:~/blog-src/compile-basic/2# ./a.out
return...
```



## 重定位

### **地位**

 ![Screen Shot 2020-05-06 at 1.31.43 AM](/images/compile-basic/Screen%20Shot%202020-05-06%20at%201.31.43%20AM.png)

### **过程**

 ![image-20200510143953490](/images/compile-basic/image-20200510143953490.png)

 ![image-20200510144053291](/images/compile-basic/image-20200510144053291.png)

 ![image-20200510144121973](/images/compile-basic/image-20200510144121973.png)



### **举例说明**

下面用一个具体的例子来看一下重定位修正符号地址的过程

源码路径：[main.c][6] [sub.c][7]

```shell
root@xmalloc:~/blog-src/compile-basic/3# arm-linux-gnueabi-gcc -c main.c sub.c
root@xmalloc:~/blog-src/compile-basic/3# ls
main.c  main.o  sub.c  sub.o
```

编译上面两个源文件，生成 `main.o` 和 `sub.o` 两个目标文件，然后通过`readelf -s`查看这两个目标文件的符号表：

 ![image-20200510135538194](/images/compile-basic/image-20200510135538194.png)

 ![image-20200510135640650](/images/compile-basic/image-20200510135640650.png)

从上面的截图中可以看到，`add,sub,mul,div` 这4个函数在 `sub.o` 中是有地址的，但是在 `main.o` 中地址值都为0，这是因为这4个函数只在 `main.c` 中声明，在 `sub.c` 中定义，在编译阶段，函数的地址是无法确定的，只有在链接阶段才能确定。

下面继续反汇编查看一下 `main.o` 目标文件：

 ![image-20200510140524790](/images/compile-basic/image-20200510140524790.png)

通过反汇编也可以看到，编译阶段，对于无法确定符号地址的那4个函数，它们在指令中的跳转地址都是0。

编译阶段对于这些找不到地址的符号，会将这些符号搜集到重定位表中，下面可以通过 `readelf -r main.o` 看一下重定位表中的内容：

 ![image-20200510141044980](/images/compile-basic/image-20200510141044980.png)

 结合上面反汇编的结果，可以看到重定位表中 `Offset` 那一列的值对应的就是 `main.o` 的目标文件中的需要重新确定指令跳转位置的偏移。

下面执行链接过程，将上面生成的两个目标文件链接形成可执行文件：

 ![image-20200510142228569](/images/compile-basic/image-20200510142228569.png)

首先通过 `readelf -s a.out` 看一下可执行文件的符号表，确定那4个函数符号的地址是不是已经得到修正：

 ![image-20200510142507069](/images/compile-basic/image-20200510142507069.png)

显然，那4个函数的地址得到了修正。接着 `arm-linux-gnueabi-objdump -D a.out` 反汇编看一下可执行文件：

 ![image-20200510143137478](/images/compile-basic/image-20200510143137478.png)

可以看到，与 `main.o` 目标文件不同的是，`add` 符号的地址从0变成了1045c



**思考**：为什么可执行文件中跳转到 `add` 函数执行，从`bl(10428)`地址到`add(1045c)`，实际是相差`0xd`个指令地址，但是在`10428`的指令`eb00000b`中，相对寻址的值为`0xb`个指令地址？

提示：arm三级流水线



# 程序的运行

两种可执行文件：

​		操作系统环境：`ELF `文件

​		裸机环境：`BIN/HEX `文件

## ELF文件的加载

操作系统环境下，首先会通过加载器，加载器将可执行文件加载到内存中，加载器拷贝数据结束，就会跳转到程序入口地址处运行该程序。

### 实例：Linux内存映像

 ![image-20200510152930367](/images/compile-basic/image-20200510152930367.png)

程序入口：

可以通过 `readlelf -h` 命令查看程序的入口地址，加载器会读取ELF文件头，找到程序入口地址

 ![image-20200510153742352](/images/compile-basic/image-20200510153742352.png) 

通过执行 `readelf -s a.out` 命令可以看上面获取的入口地址处对应的入口函数是 `_start` 。

 ![image-20200510153949738](/images/compile-basic/image-20200510153949738.png)

## BIN文件的加载

裸机环境上面运行二进制文件，需要借助 `U-boot/BIOS` 等第三方工具的帮助，将二进制文件加载到内存指定地址。



# main函数入口分析

思考：为什么c程序的入口函数是main?

源码文件：main.c main1.c

```shell
root@xmalloc:~/blog-src/compile-basic/12# gcc main.c -o 1.out
root@xmalloc:~/blog-src/compile-basic/12# ls
1.out  main.c  main1.c
root@xmalloc:~/blog-src/compile-basic/12# ./1.out
hello world
root@xmalloc:~/blog-src/compile-basic/12# gcc main1.c -o 2.out
/usr/lib/gcc/x86_64-linux-gnu/7/../../../x86_64-linux-gnu/Scrt1.o: In function `_start':
(.text+0x20): undefined reference to `main'
collect2: error: ld returned 1 exit status
```

第一个源码文件中包含`main` 函数，编译链接后执行能正常运行，第二个源码文件中不含有 `main` 函数，在链接过程中就报错，`_start` 中 `main` 函数找不到定义。首先看一下编译器的默认链接脚本：

 ![image-20200531213006932](/images/compile-basic/image-20200531213006932.png)

从上面默认的链接脚本中知道 `_start` 是可执行文件的入口函数，这个`_start`  



从上面的链接报错信息中也应该可以推断，`main` 会在入口函数`_start` 中被调用。



下面可以进一步查看glibc的源码来验证这个推断是否正确。





# 链接静态库

## 基本定义

静态库就是目标文件的归档，一组目标文件的合集

## 制作静态库

```shell
ar rcs libtest.a a.o b.o ...
```

## 链接静态库

```shell
gcc main.c -I ./include -L ./lib -ltest
```

## 实例

源码文件：[main.c][12] [math.c][13] [add.c][9] [sub.c][10] [mul.c][11] [div.c][12]

将math.c变成静态库libmath.o，main.c链接该库文件，形成可执行文件并运行。

```shell
root@xmalloc:~/blog-src/compile-basic/5# gcc -c math.c
root@xmalloc:~/blog-src/compile-basic/5# ls
add.c  div.c  main.c  math.c  math.o  mul.c  sub.c
root@xmalloc:~/blog-src/compile-basic/5# ar rcs libmath.a math.o
root@xmalloc:~/blog-src/compile-basic/5# ls
add.c  div.c  libmath.a  main.c  math.c  math.o  mul.c  sub.c
root@xmalloc:~/blog-src/compile-basic/5# gcc main.c -L. -lmath
root@xmalloc:~/blog-src/compile-basic/5# ls
a.out  add.c  div.c  libmath.a  main.c  math.c  math.o  mul.c  sub.c
root@xmalloc:~/blog-src/compile-basic/5# ./a.out
sum = 3
```

**问题**

由于链接的基本单位是目标文件，引用一个目标文件中的符号，链接器会将整个目标文件进行链接，这导致在上面的方法中，会导致链入了目标文件中没有用到的符号，可执行文件体积过大

**解决办法**

参考glibc的实现，将每个函数的实现都使用一个源文件，生成对应的目标文件，再将所有的目标文件归档。

```shell
root@xmalloc:~/blog-src/compile-basic/5# gcc -c add.c sub.c mul.c div.c
root@xmalloc:~/blog-src/compile-basic/5# ls
a.out  add.c  add.o  div.c  div.o  libmath.a  main.c  math.c  math.o  mul.c  mul.o  sub.c  sub.o
root@xmalloc:~/blog-src/compile-basic/5# ar rcs libmath2.a add.o sub.o mul.o div.o
root@xmalloc:~/blog-src/compile-basic/5# ls
a.out  add.c  add.o  div.c  div.o  libmath.a  libmath2.a  main.c  math.c  math.o  mul.c  mul.o  sub.c  sub.o
root@xmalloc:~/blog-src/compile-basic/5# gcc main.c -L. -lmath2 -o a2.out
root@xmalloc:~/blog-src/compile-basic/5# ls
a.out  a2.out  add.c  add.o  div.c  div.o  libmath.a  libmath2.a  main.c  math.c  math.o  mul.c  mul.o  sub.c  sub.o
root@xmalloc:~/blog-src/compile-basic/5# ./a2.out
sum = 3
```

可以通过 `readelf -s`  命令比较 `a.out`  和 `a2.out` 符号表。



# 动态链接

静态链接在链接过程中，将目标文件都链接进可执行文件，这样会导致生成的可执行文件体积较大，浪费存储空间，运行时占用内存较大的问题。

动态链接则推迟到运行时才进行，动态链接目标文件被称为动态链接库，程序运行时，除了可执行文件，这些动态库也要被加载到内存，进行重定位。

动态链接相对静态库，解决了内存空间浪费的问题，节省了内存；同时，当库文件发生改变时，不需要重新编译可执行文件，使得升级更加容易方便。

## 过程

 ![image-20200510205332412](/images/compile-basic/image-20200510205332412.png)

## 装载时重定位

 ![image-20200510205835889](/images/compile-basic/image-20200510205835889.png)

**缺点**

装载时重定位，对于每个进程，共享库都链接到一个不同的地址，导致动态库无法在多个进程之间共享，也无法节省内存，本质上和静态库是一样的，只不过将符号重定位操作推迟到加载之后进行。

**解决办法**

与地址无关的代码

## 地址无关代码

 ![image-20200510211952670](/images/compile-basic/image-20200510211952670.png)

**PIC底层技术支撑**

GOT：全局偏移表

|          | 各种地址引用方式    |               |
| -------- | ------------------- | ------------- |
|          | 指令跳转、调用      | 数据访问      |
| 模块内部 | 相对跳转和调用      | 相对地址访问  |
| 模块外部 | 间接跳转和调用(GOT) | 间接访问(GOT) |

## 全局偏移表

动态链接的核心就是修改GOT的实际地址



## 实例

源码文件：[main.c][12]  [sub.c][9]

```shell
root@xmalloc:~/blog-src/compile-basic/5# gcc add.c -shared -fPIC -o libadd.so
root@xmalloc:~/blog-src/compile-basic/5# ls
add.c  div.c  libadd.so  main.c  math.c  mul.c  sub.c
root@xmalloc:~/blog-src/compile-basic/5# gcc main.c -L. -ladd
root@xmalloc:~/blog-src/compile-basic/5# ls
a.out  add.c  div.c  libadd.so  main.c  math.c  mul.c  sub.c
root@xmalloc:~/blog-src/compile-basic/5# ldd a.out
	linux-vdso.so.1 (0x00007fff4357a000)
	libadd.so => not found
	libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007ff4383fe000)
	/lib64/ld-linux-x86-64.so.2 (0x00007ff4389f1000)
root@xmalloc:~/blog-src/compile-basic/5# ./a.out
./a.out: error while loading shared libraries: libadd.so: cannot open shared object file: No such file or directory
root@xmalloc:~/blog-src/compile-basic/5# export LD_LIBRARY_PATH=./
root@xmalloc:~/blog-src/compile-basic/5# ./a.out
sum = 3
```

# 插件



# 内核模块加载机制



# binutils工具

| 工具名     | 用途                                                     |
| ---------- | -------------------------------------------------------- |
| as         | 汇编器，将汇编文件汇编为目标文件                         |
| ld         | 链接器，将几个目标文件和库组合成一个文件                 |
| nm         | 列出目标文件中的符号                                     |
| size       | 列出目标文件中的各个段的大小和总大小，如代码段、数据段等 |
| strip      | 移除目标文件中的符号                                     |
| gprof      | 显示分析数据的调用图表                                   |
| ar         | 创建、修改和提取归档目标文件(静态库)                     |
| addr2line  | 将程序地址翻译成文件名和行号                             |
| objcopy    | 将一种目标文件翻译成另一种：.bin<-->.elf                 |
| objdump    | 显示目标文件的信息、反汇编                               |
| readelf    | 显示有关ELF文件的信息                                    |
| ranlib     | 不常用/RTFM                                              |
| strings    | 不常用/RTFM                                              |
| libopcodes | 不常用/RTFM                                              |



[1]:https://github.com/hahalaoshi/blog-src/blob/master/compile-basic/1/main.c
[2]:https://github.com/hahalaoshi/blog-src/blob/master/compile-basic/1/sub.c
[3]:https://github.com/hahalaoshi/blog-src/blob/master/compile-basic/2/main.c
[4]: https://github.com/hahalaoshi/blog-src/blob/master/compile-basic/2/libdecode.c
[5]: https://github.com/hahalaoshi/blog-src/blob/master/compile-basic/2/decode.h
[6]: https://github.com/hahalaoshi/blog-src/blob/master/compile-basic/3/main.c
[7]: https://github.com/hahalaoshi/blog-src/blob/master/compile-basic/3/sub.c
[8]: https://github.com/hahalaoshi/blog-src/blob/master/compile-basic/5/add.c
[9]: https://github.com/hahalaoshi/blog-src/blob/master/compile-basic/5/sub.c
[10]: https://github.com/hahalaoshi/blog-src/blob/master/compile-basic/5/mul.c
[11]: https://github.com/hahalaoshi/blog-src/blob/master/compile-basic/5/div.c
[12]: https://github.com/hahalaoshi/blog-src/blob/master/compile-basic/5/main.c
[13]: https://github.com/hahalaoshi/blog-src/blob/master/compile-basic/5/math.c