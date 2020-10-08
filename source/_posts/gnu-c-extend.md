---
title: GNU-C扩展语法介绍
date: 2020-04-29 22:24:49
tags:
  - 编译器
  - gnu-c
typora-root-url: ../../source
---

# 工具介绍

## readelf

可以通过readelf这个工具，来看一个可执行文件中各个section的信息。

```shell
#Section header table
readelf -S a.out
#Symbol table
readelf -s a.out
```



# 关键字attribute

GNU C 增加一个  `__atttribute__ ` 关键字用来声明一个函数、变量或类型的特殊属性。声明这个特殊属性有什么用呢？主要用途就是指导编译器在编译程序时进行特定方面的优化或代码检查。比如，我们可以通过使用属性声明指定某个变量的数据边界对齐方式。

使用方法：

```
__atttribute__((ATTRIBUTE))
```

ATTRIBUTE就是要声明的属性，主要包括下面这些：

- section
- aligned
- packed
- format
- weak
- alias
- noinline
- always_inline
- ……



## section

### 场景1：变量放入指定段中

一般默认规则下**组成**代码段( .text)函数定义、程序语句数据段( .data)初始化的全局变量、初始化的静态局部变量BSS段( .bss)未初始化的全局变量、未初始化的静态局部变量

在这个场景中，通过section字段，将**未初始化的全局变量**放入.data中：

```C
int global_val = 8;
int uninit_val __attribute__((section(".data")));
int main(void)
{
    return 0;
}
```

通过readelf可以查看一下uninit_val被放在哪个段中：

```shell
root@xmalloc:~# readelf -s a.out

Symbol table '.dynsym' contains 6 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND
     1: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_deregisterTMCloneTab
     2: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND __libc_start_main@GLIBC_2.2.5 (2)
     3: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
     4: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_registerTMCloneTable
     5: 0000000000000000     0 FUNC    WEAK   DEFAULT  UND __cxa_finalize@GLIBC_2.2.5 (2)

Symbol table '.symtab' contains 63 entries:
   Num:    Value          Size Type    Bind   Vis      Ndx Name
     0: 0000000000000000     0 NOTYPE  LOCAL  DEFAULT  UND
     1: 0000000000000238     0 SECTION LOCAL  DEFAULT    1
     2: 0000000000000254     0 SECTION LOCAL  DEFAULT    2
     3: 0000000000000274     0 SECTION LOCAL  DEFAULT    3
     4: 0000000000000298     0 SECTION LOCAL  DEFAULT    4
     5: 00000000000002b8     0 SECTION LOCAL  DEFAULT    5
     6: 0000000000000348     0 SECTION LOCAL  DEFAULT    6
     7: 00000000000003c6     0 SECTION LOCAL  DEFAULT    7
     8: 00000000000003d8     0 SECTION LOCAL  DEFAULT    8
     9: 00000000000003f8     0 SECTION LOCAL  DEFAULT    9
    10: 00000000000004b8     0 SECTION LOCAL  DEFAULT   10
    11: 00000000000004d0     0 SECTION LOCAL  DEFAULT   11
    12: 00000000000004e0     0 SECTION LOCAL  DEFAULT   12
    13: 00000000000004f0     0 SECTION LOCAL  DEFAULT   13
    14: 0000000000000684     0 SECTION LOCAL  DEFAULT   14
    15: 0000000000000690     0 SECTION LOCAL  DEFAULT   15
    16: 0000000000000694     0 SECTION LOCAL  DEFAULT   16
    17: 00000000000006d0     0 SECTION LOCAL  DEFAULT   17
    18: 0000000000200df0     0 SECTION LOCAL  DEFAULT   18
    19: 0000000000200df8     0 SECTION LOCAL  DEFAULT   19
    20: 0000000000200e00     0 SECTION LOCAL  DEFAULT   20
    21: 0000000000200fc0     0 SECTION LOCAL  DEFAULT   21
    22: 0000000000201000     0 SECTION LOCAL  DEFAULT   22
    23: 0000000000201018     0 SECTION LOCAL  DEFAULT   23
    24: 0000000000000000     0 SECTION LOCAL  DEFAULT   24
    25: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS crtstuff.c
    26: 0000000000000520     0 FUNC    LOCAL  DEFAULT   13 deregister_tm_clones
    27: 0000000000000560     0 FUNC    LOCAL  DEFAULT   13 register_tm_clones
    28: 00000000000005b0     0 FUNC    LOCAL  DEFAULT   13 __do_global_dtors_aux
    29: 0000000000201018     1 OBJECT  LOCAL  DEFAULT   23 completed.7697
    30: 0000000000200df8     0 OBJECT  LOCAL  DEFAULT   19 __do_global_dtors_aux_fin
    31: 00000000000005f0     0 FUNC    LOCAL  DEFAULT   13 frame_dummy
    32: 0000000000200df0     0 OBJECT  LOCAL  DEFAULT   18 __frame_dummy_init_array_
    33: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS test.c
    34: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS crtstuff.c
    35: 00000000000007d4     0 OBJECT  LOCAL  DEFAULT   17 __FRAME_END__
    36: 0000000000000000     0 FILE    LOCAL  DEFAULT  ABS
    37: 0000000000200df8     0 NOTYPE  LOCAL  DEFAULT   18 __init_array_end
    38: 0000000000200e00     0 OBJECT  LOCAL  DEFAULT   20 _DYNAMIC
    39: 0000000000200df0     0 NOTYPE  LOCAL  DEFAULT   18 __init_array_start
    40: 0000000000000694     0 NOTYPE  LOCAL  DEFAULT   16 __GNU_EH_FRAME_HDR
    41: 0000000000200fc0     0 OBJECT  LOCAL  DEFAULT   21 _GLOBAL_OFFSET_TABLE_
    42: 0000000000000680     2 FUNC    GLOBAL DEFAULT   13 __libc_csu_fini
    43: 0000000000201014     4 OBJECT  GLOBAL DEFAULT   22 uninit_val
    44: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_deregisterTMCloneTab
    45: 0000000000201000     0 NOTYPE  WEAK   DEFAULT   22 data_start
    46: 0000000000201018     0 NOTYPE  GLOBAL DEFAULT   22 _edata
    47: 0000000000000684     0 FUNC    GLOBAL DEFAULT   14 _fini
    48: 0000000000201010     4 OBJECT  GLOBAL DEFAULT   22 global_val
    49: 0000000000000000     0 FUNC    GLOBAL DEFAULT  UND __libc_start_main@@GLIBC_
    50: 0000000000201000     0 NOTYPE  GLOBAL DEFAULT   22 __data_start
    51: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND __gmon_start__
    52: 0000000000201008     0 OBJECT  GLOBAL HIDDEN    22 __dso_handle
    53: 0000000000000690     4 OBJECT  GLOBAL DEFAULT   15 _IO_stdin_used
    54: 0000000000000610   101 FUNC    GLOBAL DEFAULT   13 __libc_csu_init
    55: 0000000000201020     0 NOTYPE  GLOBAL DEFAULT   23 _end
    56: 00000000000004f0    43 FUNC    GLOBAL DEFAULT   13 _start
    57: 0000000000201018     0 NOTYPE  GLOBAL DEFAULT   23 __bss_start
    58: 00000000000005fa    11 FUNC    GLOBAL DEFAULT   13 main
    59: 0000000000201018     0 OBJECT  GLOBAL HIDDEN    22 __TMC_END__
    60: 0000000000000000     0 NOTYPE  WEAK   DEFAULT  UND _ITM_registerTMCloneTable
    61: 0000000000000000     0 FUNC    WEAK   DEFAULT  UND __cxa_finalize@@GLIBC_2.2
    62: 00000000000004b8     0 FUNC    GLOBAL DEFAULT   10 _init
```

```shell
root@xmalloc:~# readelf -S a.out
There are 28 section headers, starting at offset 0x1930:

Section Headers:
  [Nr] Name              Type             Address           Offset
       Size              EntSize          Flags  Link  Info  Align
  [ 0]                   NULL             0000000000000000  00000000
       0000000000000000  0000000000000000           0     0     0
  [ 1] .interp           PROGBITS         0000000000000238  00000238
       000000000000001c  0000000000000000   A       0     0     1
  [ 2] .note.ABI-tag     NOTE             0000000000000254  00000254
       0000000000000020  0000000000000000   A       0     0     4
  [ 3] .note.gnu.build-i NOTE             0000000000000274  00000274
       0000000000000024  0000000000000000   A       0     0     4
  [ 4] .gnu.hash         GNU_HASH         0000000000000298  00000298
       000000000000001c  0000000000000000   A       5     0     8
  [ 5] .dynsym           DYNSYM           00000000000002b8  000002b8
       0000000000000090  0000000000000018   A       6     1     8
  [ 6] .dynstr           STRTAB           0000000000000348  00000348
       000000000000007d  0000000000000000   A       0     0     1
  [ 7] .gnu.version      VERSYM           00000000000003c6  000003c6
       000000000000000c  0000000000000002   A       5     0     2
  [ 8] .gnu.version_r    VERNEED          00000000000003d8  000003d8
       0000000000000020  0000000000000000   A       6     1     8
  [ 9] .rela.dyn         RELA             00000000000003f8  000003f8
       00000000000000c0  0000000000000018   A       5     0     8
  [10] .init             PROGBITS         00000000000004b8  000004b8
       0000000000000017  0000000000000000  AX       0     0     4
  [11] .plt              PROGBITS         00000000000004d0  000004d0
       0000000000000010  0000000000000010  AX       0     0     16
  [12] .plt.got          PROGBITS         00000000000004e0  000004e0
       0000000000000008  0000000000000008  AX       0     0     8
  [13] .text             PROGBITS         00000000000004f0  000004f0
       0000000000000192  0000000000000000  AX       0     0     16
  [14] .fini             PROGBITS         0000000000000684  00000684
       0000000000000009  0000000000000000  AX       0     0     4
  [15] .rodata           PROGBITS         0000000000000690  00000690
       0000000000000004  0000000000000004  AM       0     0     4
  [16] .eh_frame_hdr     PROGBITS         0000000000000694  00000694
       000000000000003c  0000000000000000   A       0     0     4
  [17] .eh_frame         PROGBITS         00000000000006d0  000006d0
       0000000000000108  0000000000000000   A       0     0     8
  [18] .init_array       INIT_ARRAY       0000000000200df0  00000df0
       0000000000000008  0000000000000008  WA       0     0     8
  [19] .fini_array       FINI_ARRAY       0000000000200df8  00000df8
       0000000000000008  0000000000000008  WA       0     0     8
  [20] .dynamic          DYNAMIC          0000000000200e00  00000e00
       00000000000001c0  0000000000000010  WA       6     0     8
  [21] .got              PROGBITS         0000000000200fc0  00000fc0
       0000000000000040  0000000000000008  WA       0     0     8
  [22] .data             PROGBITS         0000000000201000  00001000
       0000000000000018  0000000000000000  WA       0     0     8
  [23] .bss              NOBITS           0000000000201018  00001018
       0000000000000008  0000000000000000  WA       0     0     1
  [24] .comment          PROGBITS         0000000000000000  00001018
       000000000000002b  0000000000000001  MS       0     0     1
  [25] .symtab           SYMTAB           0000000000000000  00001048
       00000000000005e8  0000000000000018          26    42     8
  [26] .strtab           STRTAB           0000000000000000  00001630
       0000000000000206  0000000000000000           0     0     1
  [27] .shstrtab         STRTAB           0000000000000000  00001836
       00000000000000f9  0000000000000000           0     0     1
```

![Screen Shot 2020-04-30 at 12.31.13 AM](../images/gnu-c-extend/Screen%20Shot%202020-04-30%20at%2012.31.13%20AM.png)![Screen Shot 2020-04-30 at 12.29.50 AM](../images/gnu-c-extend/Screen%20Shot%202020-04-30%20at%2012.29.50%20AM.png)



### 场景2：函数放入指定段中

下列代码将函数放入用户指定的section:

```c
__attribute__((section(".text")))int func(void)
{
    return 0;
}
```

Tip：

​	函数和变量不应该放同一个section中

### 应用

linux内核驱动中的__init宏

```c
/* include/linux/init.h#L50 */
#define __init		__section(.init.text) __cold  __latent_entropy __noinitretpoline
#define __initdata	__section(.init.data)
#define __initconst	__section(.init.rodata)
#define __exitdata	__section(.exit.data)
```

linux中驱动开发时，驱动的初始化化函数都会被__init宏修饰，这个目的主要是将初始化函数放到特定的section，由于初始化函数只被执行一次，将这些只执行一次的初始化函数放到统一的section中，到时候可以被统一释放内存。

一般linux内核启动过程中会打印下列日志：

![Screen Shot 2020-04-30 at 12.51.31 AM](/images/gnu-c-extend/Screen%20Shot%202020-04-30%20at%2012.51.31%20AM.png)

在linux的kernel_init函数中，会调用free_initmem函数，该函数会释放初始化函数所在section占用的内存

**TODO**: 

1. 进一步分析linux的链接脚本vmlinux.lds

2. U-boot重定位过程--u-boot是如何将自身代码拷贝到RAM中的



## aligned & packed

 **自然对齐**：如果一个变量的内存地址正好位于它长度的整数倍，他就被称做自然对齐。比如在32位cpu下，假设一个整型变量的地址为0x00000004，那它就是自然对齐的。

下面请看结构体类型自然边界对齐的例子：

```c
#include <stdio.h>
struct data {
        char a;
        int b;
        short c;
};

int main(void)
{
        struct data s;
        printf("size: %d\n", sizeof(s));
        printf("a: %p\n", &s.a);
        printf("b: %p\n", &s.b);
        printf("c: %p\n", &s.c);
}
```

```shell
root@xmalloc:~/align# ./a.out
size: 12
a: 0x7ffdff53b6ec
b: 0x7ffdff53b6f0
c: 0x7ffdff53b6f4
```

```c
#include <stdio.h>
struct data {
        char a;
        short b;
        int c;
};

int main(void)
{
        struct data s;
        printf("size: %d\n", sizeof(s));
        printf("a: %p\n", &s.a);
        printf("b: %p\n", &s.b);
        printf("c: %p\n", &s.c);
}
```

```shell
root@xmalloc:~/align# ./a.out
size: 8
a: 0x7ffc8e1ad890
b: 0x7ffc8e1ad892
c: 0x7ffc8e1ad894
```

结构体变量中元素的不同位置排列，会影响结构的size大小，因为结构体中变量位置不同，按照自然边界对齐的方式，会产生不同的内存空洞。

### aligned

通过GNU-C扩展语法中的aligned属性，可以设置内存对齐方式：

\__attribute__((aligned(n))) 指定结构体类型、联合类型、变量为n字节对齐，其中n为2的幂

```c
#include <stdio.h>
struct data {
        char a;
        short b;
        int c;
}__attribute__((aligned(16)));

int main(void)
{
        struct data s;
        printf("size: %d\n", sizeof(s));
        printf("a: %p\n", &s.a);
        printf("b: %p\n", &s.b);
        printf("c: %p\n", &s.c);
}
```

```shell
root@xmalloc:~/align# ./a.out
size: 16
a: 0x7ffdeca3fb20
b: 0x7ffdeca3fb22
c: 0x7ffdeca3fb24
```

Q：编译器一定会按照aligned指定的值对齐吗？

A：不会，编译器会设定对齐的值的最大值，超过这个值的对齐方式都是按照最大值来对齐的。

Q：内核中\__attribute__((aligned))是按多少对齐？

A：不指定n的值，一般就是按照最大字节对齐。

### packed

通过GNU-C扩展语法中的packed属性，可以减少元素之间因边界对齐而造成的内存空洞，：

\__attribute__((packed)) 指定变量和类型（结构体、联合、枚举）使用最小可能的对齐

```c
#include <stdio.h>
struct data {
        char a;
        short b __attribute__((packed));
        int c __attribute__((packed));
};

int main(void)
{
        struct data s;
        printf("size: %d\n", sizeof(s));
        printf("a: %p\n", &s.a);
        printf("b: %p\n", &s.b);
        printf("c: %p\n", &s.c);
}
```



```shell
root@xmalloc:~/align# ./a.out
size: 7
a: 0x7fff8582f611
b: 0x7fff8582f612
c: 0x7fff8582f614
```

## format

## const

该属性声明的作用主要是对于一些结果确定的函数调用，将函数结果保存起来，多次调用函数时，避免函数调用的开销，直接将函数结果返回，下面通过一个具体的例子来做解释：

```c
#include <stdio.h>
int square(int n) __attribute__((const));
int square(int n)
{
    printf("square:\n");
    return n * n;
}
int main(void)
{
    int sum = 0;
    for (int i = 0; i < 10; i++) {
        sum += square(4);
    }
    printf("sum = %d\n", sum);
    return 0;
}
```

然后通过下面的命令编译上面示例代码：

```shell
gcc demo.c -O2
```

执行代码，显示下面的结果：

```shell

```

#### 应用

查找linux中带const属性的函数

# 内建函数

## \__builtin_return_address(LEVEL)

定义：

- 返回当前函数或者调用者的返回地址
- 0：表示当前函数的返回地址
- 1：表示当前函数的调用者的返回地址



## \__builtin_frame_address(LEVEL)

定义：

- 返回函数的栈帧地址
- 0：表示当前函数
- 1：表示当前函数的调用者



## __builtin_constant_p





## \__builtin_expect

定义：

- __builtin_expect(exp, c);
- 返回值：exp
- 意义：表示exp的值为c的可能性很大

**应用**：

likely: #define likely(x) __builtin_expect(!!(x), 1)

unlikely：#define likely(x) __builtin_expect(!!(x), 0)

TODO：分支条件加likely后反汇编查看指令位置差异