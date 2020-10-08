---
title: docker入门
date: 2020-03-08 12:53:30
tags: 
	- docker
	- tutorial
typora-root-url: ../../source
---

自己学习docker的一些记录，主要包括一些常用命令的介绍和使用，dockerfile详解，构建一个自己的docker镜像

# docker介绍

# docker容器

## docker容器的分类

容易可以分为两类，一类是交互式容器，还有一类是能保持长期运行的守护式容器，守护式容器没有交互式会话非常适合运行应用程序和服务，而且大多数时候我们更需要这种守护式容器。在这一节中会分别介绍这两种容器的一些操作命令，同时会介绍容器存在哪些状态，并给出不同命令下的容器状态转换图。

### 交互式容器常用操作命令

#### 运行容器

```powershell
sudo docker run -it ubuntu /bin/bash
```

参数解释：

`-i` 开启容器中的STDIN

`-t` 为容器分配一个伪终端

`ubuntu` 基础镜像

`/bin/bash` 容器中要执行的命令

#### 容器命名

```powershell
sudo docker run --name my_container -it ubuntu /bin/bash
```

`name` 通过这个参数给容器起一个名称，以替代容器ID，并且有助于分辨容器。

#### 启动容器

```powershell
sudo docker start my_container
```

#### 查看容器状态信息

```powershell
sudo docker ps -a
```

#### 附着到容器

```powershell
sudo docker attach my_container
```

#### 删除容器

```powershell
#删除指定容器
sudo docker rm my_container
```

### 守护式容器常用操作命令

#### 创建守护式容器

```powershell
sudo docker run --name daemon_container -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

参数解释：

`-d` daemon

#### 获取守护式容器日志

```powershell
sudo docker logs daemon_container -ft
```

#### 获取守护式容器内进程

```powershell
sudo docker top daemon_container
```

或者用 `stats` 命令

```powershell
sudo stats container_name1 container_name2
```

#### 容器内运行进程

运行后台任务：

```powershell
sudo docker exec -d daemon_container touch /etc/new_config_file
```

这里 `-d` 表示需要运行一个后台进程

运行交互任务：

```powershell
sudo docker exec -it daemon_container /bin/bash
```

#### 停止守护式容器

```powershell
sudo docker stop daemon_container
```

#### 容器自动重启

```powershell
sudo docker run --restart=always --name daemon_container -d ubuntu /bin/sh -c "while true; do echo hello world; sleep 1; done"
```

或者 `--restart=on-failure:5` , 当容器退出代码为非0时，Docker会尝试自动重启该容器，最多重启5次

## 容器状态转换图

![20170911155150639](/images/docker-tutorial/20170911155150639-3860393.png)

# docker镜像与dockerfile





# docker应用