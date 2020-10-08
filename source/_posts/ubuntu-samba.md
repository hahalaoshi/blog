---
title: ubuntu18.04上samba配置记录
date: 2020-10-08 20:56:52
tags:
typora-root-url: ../../source
---

本文主要记录一下在ubuntu18.04上配置samba，并且将ubuntu上的home目录通过映射网络驱动器的方式在windows侧访问。



# 准备

```shell
sudo apt install samba
```

# 配置

## 修改配置文件

```shell
sudo cp /etc/samba/smb.conf /etc/samba/smb.conf_backup
sudo bash -c 'grep -v -E "^#|^;" /etc/samba/smb.conf_backup | grep . > /etc/samba/smb.conf'
```

上面的第二条命令主要是将 `smb.conf` 中的注释行删除掉，执行完上面的命令之后的配置文件如下图：

![image-20201008214142608](/images/ubuntu-samba/image-20201008214142608.png)

最后为了映射  `home` 目录，需要编辑 `smb.conf` 配置文件，额外添加下面的几行：

```shell
[homes]
   comment = Home Directories
   browseable = yes
   read only = no
   create mask = 0700
   directory mask = 0700
   valid users = %S
```

## 添加用户

```shell
sudo smbpasswd -a haha
```

通过上面的命令添加一个 `samba` 用户，在windows上映射时需要填用户名和密码。

## 重启服务

任何对 `smb.conf` 文件的修改都要通过下面的命令重启一下 `samba` 服务，使得新的配置生效。

```shell
sudo systemctl restart smbd
```

# 挂载

上面的步骤都是在ubuntu侧完成的，下面所有关于映射的操作都需要在windows上完成。

![image-20201008215323784](/images/ubuntu-samba/image-20201008215323784.png)

![image-20201008215439434](/images/ubuntu-samba/image-20201008215439434.png)

最后如果像下图这样，说明 `samba` 映射挂载 `home` 目录已经成功了，windows上就可以访问ubuntu上 `home` 目录下的所有文件了

![image-20201008215503100](/images/ubuntu-samba/image-20201008215503100.png)