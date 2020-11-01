---
title: 通过buildroot构建aarch64根文件系统
date: 2020-11-01 14:05:46
tags:
  - aarch64
  - filesystem
  - buildroot
typora-root-url: ../../source
---



这篇文章主要是记录一下怎么通过buildroot工具来构建一个arm64-linux可以使用的文件系统。buildroot工具相关的介绍和使用可以自行查阅相关资料获取，这里就不详细介绍了，本文主要是记录一些配置信息。

# buildroot配置

通过下面命令进入配置界面：

```shell
make menuconfig
```

进入配置界面后参考下面的截图进行设置。

### Target options

![image-20201101141848594](/images/filesystem-buildroot/image-20201101141848594.png)

### Build options

![image-20201101142834953](/images/filesystem-buildroot/image-20201101142834953.png)

### Toolchain

![image-20201101142958211](/images/filesystem-buildroot/image-20201101142958211.png)

### System configuration 

![image-20201101143059549](/images/filesystem-buildroot/image-20201101143059549.png)

### Filesystem images 

![image-20201101143159045](/images/filesystem-buildroot/image-20201101143159045.png)

# 构建文件系统

通过下面的命令可以构建生成根文件系统：

```shell
make -j16
```

![image-20201101144418285](/images/filesystem-buildroot/image-20201101144418285.png)

待编译完成可以看到在 `output\images` 目录下生成了根文件 `rootfs.cpio.gz` ，配合编译出来的kernel镜像，就可以拉起linux，并进入shell了，这样就完成了aarch64开发环境的搭建。