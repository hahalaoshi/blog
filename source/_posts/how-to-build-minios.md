---
title: how-to-build-minios
tags: 
	- os
	- linux
	- tool-chain
date: 2020-03-04 01:21:26
typora-root-url: ../../source
---

# 准备工作

## 编译工具

### 交叉编译器

两种安装方式，一通过apt

```

```



### kernel编译工具

1. 必备工具

   ```shell
   sudo apt-get install bison
   sudo apt-get install flex
   ```

   

2. 设备树解析工具

   ```shell
   sudo apt-get install device-tree-compiler
   ```

   

# 构建minios

# 运行minios

# 问题说明

## Makefile

在makefile中，用到了 `pushd` 、`popd` 这两个内建命令，编译过程中可能会报下面的错误：

```shell
/bin/sh: 1: pushd: not found
```

原因：ubuntu中sh命令链接到的是dash，而pushed命令需要在bash的环境中执行。

解决方法：

```shell
sudo dpkg-reconfigure dash
```

![bash](/images/how-to-build-minios/bash.png)

在弹出的窗口中，选择 `No` ，这样就可以将/bin/sh链接到bash了。 

