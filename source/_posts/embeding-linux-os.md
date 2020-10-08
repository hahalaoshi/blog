---
title: 嵌入式linux开发环境搭建
tags:
  - embeding-linux
  - minios
  - vexpress-cortex-a9
typora-root-url: ../../source
date: 2020-04-06 01:37:31
---


这篇文章主要是介绍怎么编译出一个能在arm单板上运行的linux最小系统，以方便后续嵌入式驱动方面的开发。

# 准备工作

## 安装交叉编译器

两种安装方式，一是通过包管理工具，ubuntu上面的apt来安装；二是下载源码安装。请根据自己需要来选择：

```shell
#apt-get安装
sudo apt-get install gcc-arm-linux-gnueabi
#源码安装
wget http://mirrors.sunsite.dk/armbian-dl/_toolchains/gcc-linaro-7.3.1-2018.05-x86_64_arm-linux-gnueabi.tar.xz
```



## 下载工具和源码

### qemu

```shell
#源码安装
wget https://download.qemu.org/qemu-4.0.1.tar.xz
#apt-get安装
sudo apt-get install qemu libncurses5-dev gcc-arm-linux-gnueabi build-essential
```



### busybox

我们主要是要通过busybox来构造一个根文件系统，可以通过下面的命令获取 `busybox` ，解压备用。

```shell
wget https://busybox.net/downloads/busybox-1.31.0.tar.bz2
```



### linux kernel

这里选用的内核版本是 `4.4.76` ,通过下面的命令下载后，解压备用。

```shell
wget https://mirror.bjtu.edu.cn/kernel/linux/kernel/v4.x/linux-4.4.76.tar.gz
```

### 

# 编译内核

通过下面的命令来编译linux内核：

```shell
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- vexpress_defconfig
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- zImage
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- modules
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- dtbs
```

执行完上面的命令应该会生成kernel镜像文件 `zImage` 和 `dtb` 文件：

 ![image-20200531204037009](/images/embeding-linux-os/image-20200531204037009.png)

在 `arch/arm/boot/dts` 下面会生成 `vexpress-v2p-ca9.dtb` 文件，这两个文件后面会用到。



# 制作根文件系统

## 编译busybox

```shell
#配置
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- defconfig
#编译
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi-
#安装
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- install
```

执行完上面第6行命令，在busybox的源码目录下面会增加一个`_install` 目录，该目录包含下面的文件:

 ![image-20200531204518205](/images/embeding-linux-os/image-20200531204518205.png)



## 制作根文件系统

```shell
mkdir rootfs
mkdir rootfs/lib
cp -r busybox/_install/* rootfs/
cp -p arm-linux-gnuabi/lib/* rootfs/lib
mkdir -p rootfs/dev/
cd rootfs/dev/
mknod -m 666 tty1 c 4 1
mknod -m 666 tty2 c 4 2
mknod -m 666 tty3 c 4 3
mknod -m 666 tty4 c 4 4
mknod -m 666 console c 5 1
mknod -m 666 null c 1 3
#用dd命令生成一个32M的磁盘文件
dd if=/dev/zero of=rootfs.ext3 bs=1M count=32
mkfs.ext3 rootfs.ext3
mount -t ext3 rootfs.ext3 /mnt -o loop
cp -r rootfs/* /mnt
umount /mnt
```

# 启动

通过下面的脚本就可以在qemu模拟的单板环境上启动一个linux内核的嵌入式系统了：

```shell
#run.sh
qemu-system-arm \
	-M vexpress-a9 \
	-m 512M \
	-kernel linux-4.4.76/arch/arm/boot/zImage \
	-dtb linux-4.4.76/arch/arm/boot/dts/vexpress-v2p-ca9.dtb \
	-nographic
	-append "root=/dev/mmcblk0 rw console=ttyAMA0" \
	-sd rootfs.ext3
```



# TODO

rc.local

u-boot启动

构建相关源码放到一个文件目录中，写一个makefile来构建