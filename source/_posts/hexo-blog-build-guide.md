---

title: ubuntu云服务器hexo博客搭建指南
date: 2020-03-08 13:44:46
tags: 
	- blog
	- hexo
typora-root-url: ../../source
---

在搬瓦工上有一个闲置的VPS，最近还申请了一个域名，就想试着用hexo搭一个静态个人博客，这篇文章记录一下搭建过程，从一个纯净的ubuntu系统开始，主要涉及下面这些内容：

1. 前置条件
2. 原理
3. 云服务器配置
4. 个人本地设置



# 前置条件

域名：1个，云主机ip绑定；

云主机：1台，ubuntu18.04系统

华为云，阿里云，腾讯云等都可以，这里选择搬瓦工主要是国外的主机搭建的站点，不需要备案

# 原理

![701869-197dfe72f7db5163.](/images/hexo%E6%90%AD%E5%BB%BA%E6%8C%87%E5%8D%97/701869-197dfe72f7db5163..png)																			<center>[hexo架构][1]</center>

在本地计算机上首先hexo通过将markdown格式的文件渲染成静态html文件，远程服务器上有一个裸git仓库，里面配置了hook，hook的作用是每次本地服务器上执行hexo部署的时候，会将推到服务器的blog网页文件，拷贝到网站根目录下，这样就完成了本地部署，服务器端的网页也会同步更新，下面会对具体步骤作详细介绍。

# 云服务器配置

## 初始化git仓库

```powershell
mkdir myblog
chown -R $USER:$USER myblog/
chmod -R 755 myblog/
cd myblog/
#建立git裸仓
git init --bare hexo_blog.git
```

## nginx环境配置

### 安装nginx

```powershell
apt-get update
apt-get install nginx
```

### 设置nginx

将hexo的index目录设置为 `/var/www/myblog`

```powershell
cd /var/www
mkdir myblog
chown -R $USER:$USER myblog/
chmod -R 755 myblog/
```

### 绑定域名

这里将域名 www.xmalloc.club 指向 `/var/www/myblog`

在 `/etc/nginx/sites-available` 创建xmalloc.club文件，写入下列文件内容

```nginx
server {
        listen 80;
        listen [::]:80;

        server_name www.xmalloc.club;

        root /var/www/myblog;
        index index.html;

        location / {
                try_files $uri $uri/ =404;
        }

    location ~ .*\.(gif|jpg|jpeg|png|bmp|swf)$ {
        access_log off;
        expires 1d;
    }
    location ~ \.(js|css) {
       access_log off;
       expires 1d;
    }
}
```

这里需要将第5行中的域名替换成你自己的域名。

```powershell
#创建软链接
ln -s /etc/nginx/sites-available/xmalloc.club /etc/nginx/sites-enabled/
#重启nginx
systemctl restart nginx
#默认index页面
cp /var/www/html/index.nginx-debian.html /var/www/myblog/index.html
```

上述第6行中将nginx的欢迎页面移到了  `/var/www/myblog ` 目录下，这时访问一下 www.xmalloc.club， 如果显示nginx的欢迎页面，说明nginx的设置生效了

## 云服务器git 钩子设置

云服务器上的git的钩子设置是为了将提交上来的文件，将其拷贝到第二步中nginx设置的域名目录

创建 `/root/myblog/hexo_blog.git/hooks/post-receive`  写入下列内容：

```powershell
#!/bin/bash
git --work-tree=/var/www/myblog --git-dir=/root/myblog/hexo_blog.git checkout -f
```

添加可执行权限

```powershell
chmod +x post-receive
```

至此服务器端的配置完成。

# 本地环境配置

## hexo安装

hexo是一个博客框架，是用nodejs写的，这里我们需要在本地的机器上安装nodejs和nodejs包管理工具npm，分别记录mac系统和ubuntu16.04系统上的安装过程

### mac系统配置

```powershell
#安装 nodejs
brew install nodejs
node -v
#安装 npm
brew install npm
npm -v
#安装 hexo
npm install -g hexo-cli
#初始化
hexo init ~/myblog
```

### ubuntu系统配置

## hexo本地博客配置

在安装完hexo之后，就可以本地创建博客目录了，这里主要分3部分介绍：

​	1. 修改 Hexo 配置中的 URL 和默认文章版式；

​	2. 新建博客草稿并发布

​	3. 配置自动部署到服务器端的 hexo_static 裸仓库

### 修改hexo配置

在4.1节中创建的 `myblog` 目录下，`_config.yml` 为hexo的主配置文件，参考下列内容进行修改：

```powershell
# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://www.xmalloc.club
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:
pretty_urls:
  trailing_index: true # Set to false to remove trailing 'index.html' from permalinks
  trailing_html: true # Set to false to remove trailing '.html' from permalinks

```

首先修改 `URL` 部分，将上面第3行修改为你自己的网站域名；

```powershell
# Writing
new_post_name: :title.md # File name of new posts
default_layout: draft
titlecase: false # Transform title into titlecase
```

接着修改 `Writing` 部分，修改 default_layout字段。将其从 post 修改为 draft ，表示每篇博文默认都是草稿，必须经过发布之后才能在博客站点上访问。

保存对配置文件的修改并退出。

### 新建博客草稿并发布

执行下列命令，创建第一篇博文

```powershell
hexo new first-post
```

出现下面的输出，表示创建成功

```powershell
INFO  Created: ~/Desktop/workspace/myblog/source/_drafts/first-post.md
```

对 `first-post.md` 进行编辑，修改后保存，通过下列命令发布博客：

```powershell
hexo publish first-post
```

出现下面输出，表示发布成功

```powershell
INFO  Published: ~/Desktop/workspace/myblog/source/_posts/first-post.md
```

至此，就差最后把博客部署到远程服务器上这一步了。

### 配置自动部署到服务器端的 hexo_static 裸仓库

修改 `_config.yml` 文件中的 `Deployment` 部分：

```powershell
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: root@你的云服务器IP地址:/root/myblog/hexo_blog.git
  branch: master
```

保存并退出，之后，还需要安装一个 Hexo 包，负责将博客所需的静态内容发送到设置好的 Git 仓库。

```powershell
npm install hexo-deployer-git --save
```

向远程服务器部署你的博客：

```powershell
hexo g -d
```

最后输出下面内容，说明远程服务器也部署成功了，可以去访问网站了。

```
INFO  Deploy done: git
```



# 问题说明

记录搭建过程中遇到的一些问题，暂无。

# 参考资料

1. [hexo部署在阿里云服务器上][1]
2. [ubuntu上部署hexo博客][2]
3. [在Ubuntu18.04服务器上部署Hexo博客][3]

[1]: https://www.jianshu.com/p/e1ccd49b4e5d "hexo部署在阿里云服务器上"
[2]: https://www.yangyanxing.com/article/hexo-deployment-in-ubuntu.html "ubuntu上部署hexo博客"
[3]: https://www.xuhuiblog.cn/Hexo/%E5%9C%A8Ubuntu18-04%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%8A%E9%83%A8%E7%BD%B2Hexo%E5%8D%9A%E5%AE%A2/ "在Ubuntu18.04服务器上部署Hexo博客"