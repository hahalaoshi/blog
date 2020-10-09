---
title: ubuntu-16.04重装系统记录
tags:
	- ubuntu
	- tool-chain
	- software install
date: 2020-03-03 00:39:48
typora-root-url: ../../source
---



本文主要是记录重装 ubuntu 之后，一些对系统配置文件的修改和软件的安装记录

# 更新源

选择清华提供的更新源，将下面所有内容拷贝到 `/etc/apt/source.list` 文件中，然后保存

```powershell
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial main restricted
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates main restricted
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-updates multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-backports main restricted universe multiverse
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security main restricted
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security universe
deb http://mirrors.tuna.tsinghua.edu.cn/ubuntu/ xenial-security multiverse
```

接着执行:

```shell
sudo apt-get update
```

让更新源生效

# 安装搜狗中文输入法

## 从官网下载输入法linux版本deb包

```shell
wget http://cdn2.ime.sogou.com/dl/index/1571302197/sogoupinyin_2.3.1.0112_amd64.deb
```

## 安装搜狗输入法

```shell
dpkg -i sogoupinyin_2.3.1.0112_amd64.deb
```

安装过程中可能会报"缺少相关依赖文件的错误"或"软件包无法下载"，可以通过执行下面的命令解决：

```shell
#缺少相关依赖文件
sudo apt-get -f install
#软件包无法下载
sudo apt-get update --fix-missing
```

安装完成后，再键盘设置里面添加搜狗拼音输入法即可使用了

# 安装typora编辑器

```shell
# or run:
# sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA300B7755AFCFAE
wget -qO - https://typora.io/linux/public-key.asc | sudo apt-key add -
# add Typora's repository
sudo add-apt-repository 'deb https://typora.io/linux ./'
sudo apt-get update
# install typora
sudo apt-get install typora
```

官网有安装命令，直接拷贝逐行执行就行

# hexo环境搭建

## 安装nodejs和npm

```shell
sudo apt-get install nodejs
sudo apt-get install nodejs-legacy
```

安装后，输入 node -v 查看版本信息

```shell
sudo apt-get install npm
```

同样，通过 `npm -v` 查看版本信息

## 安装hexo

```shell
sudo npm install -g hexo-cli
```

# 安装Docker

```shell
#卸载已安装的docker
sudo apt-get remove docker docker-engine docker-ce docker.io
#更新apt包索引
sudo apt-get update
#安装以下包以使apt可以通过HTTPS使用存储库
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common
#添加中科大Docker的GPG密钥
curl -fsSL https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu/gpg | sudo apt-key add -#设置stable存储库
sudo add-apt-repository "deb [arch=amd64] https://mirrors.ustc.edu.cn/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
#更新apt包索引
sudo apt-get update
#安装Docker CE
sudo apt-get install -y docker-ce
#验证docker服务状态
systemctl status docker
#hello world
sudo docker run hello-world
```

# 生成ssh密钥

```shell
ssh-keygen -t rsa -C "Your email address"
```

# zsh和oh-my-zsh

## 安装zsh

```shell
#安装zsh
sudo apt-get install zsh
#设置zsh为默认shell
chsh -s /bin/zsh
```

## 安装oh-my-zsh

```shell
sh -c "$(curl -fsSL https://raw.github.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"
```

## zshrc推荐配置

```powershell
#安装power fonts
sudo apt-get install fonts-powerline
```

然后在 `.zshrc` 文件中选择 `agnoster` 主题

zsh详细配置文件：

https://github.com/hahalaoshi/dotfile/tree/master/zsh

## oh-my-zsh好用插件推荐

在 `~/.oh-my-zsh/plugins` 目录下

```shell
#zsh-syntax-highlighting
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
#zsh-autosuggestions
git clone https://github.com/zsh-users/zsh-autosuggestions.git
```

# git配置

```shell
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
# 修改默认编辑器
git config --global core.editor vim
```

# vscode

## 安装

```shell
#去官网上找linux版本的deb包,并下载
https://code.visualstudio.com/
#安装
sudo dpkg -i code_1.42.1-1581432938_amd64.deb
```

## 配置

### code-snippets

https://github.com/hahalaoshi/dotfile/tree/master/vscode-snippets

### json

# 工具下载地址

## 工具链

```shell
#交叉编译器
http://releases.linaro.org/components/toolchain/binaries/7.3-2018.05/aarch64-linux-gnu/
http://mirrors.sunsite.dk/armbian-dl/_toolchains/
#qemu
https://download.qemu.org/
#busybox
https://busybox.net/downloads/
#dtc
sudo apt-get install device-tree-compiler
#bison
sudo apt-get install bison
#flex
sudo apt-get install flex
#linux kernel镜像站点
https://mirror.tuna.tsinghua.edu.cn/kernel/
```
