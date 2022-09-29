---
title: Hexo部署到个人服务器
date: 2022-09-20 13:31:46
tags:
  - Hexo
categories:
  - Hexo
keywords:
  - Hexo
abbrlink: Hexo部署到个人服务器
top_img: /img/hexo/hexo.webp
cover: /img/hexo/hexo.webp
---
# 将 Hexo 部署到自己的服务器上 （Ubuntu）

## 一、在服务器上安装 Git

```
apt install git
```

### 1、添加一个git用户

```
#添加用户
useradd git

#设置密码
passwd git

# 给git用户配置sudo权限
chmod 740 /etc/sudoers

#编辑sudoers配置文件
vim /etc/sudoers

# 找到root ALL=(ALL:ALL) ALL，在它下方加入一行:
git ALL=(ALL:ALL) ALL

chmod 400 /etc/sudoers
```

### 2、给git用户添加ssh密钥

```
su - git

mkdir -p ~/.ssh

touch ~/.ssh/authorized_keys

chmod 600 ~/.ssh/authorzied_keys

chmod 700 ~/.ssh

vim ~/.ssh/authorized_keys    #将ssh密钥粘贴进去  id_rsa.pub
```

生成ssh key方法：

```
ssh-keygen -t rsa -C "xxx@xxx.com"
```
ssh key公钥路径：C:\Users\Administrator\\.ssh\id_rsa.pub

### 3、创建git仓库并使用git-hooks实现自动部署

```
sudo mkdir -p /var/repo    #新建目录，这是git仓库的位置

sudo mkdir -p /var/www/hexo

cd /var/repo  #转到git仓库的文件夹

sudo git init --bare blog.git #创建一个名叫blog的仓库

sudo vim /var/repo/blog.git/hooks/post-update
```

post-update的内如如下：

```
#!/bin/bash
git --work-tree=/var/www/hexo --git-dir=/var/repo/blog.git checkout -f
```

### 给post-update授权

```
cd /var/repo/blog.git/hooks/

sudo chown -R git:git /var/repo/

sudo chown -R git:git /var/www/hexo

sudo chmod +x post-update  #赋予其可执行权限
```

### 4、修改git用户的默认shell环境

```
vim /etc/passwd
#修改最后一行
#将/bin/bash修改为/usr/bin/git-shell

git:x:1003:1003::/home/git:/usr/bin/git-shell
```

## 二、在服务器上安装 nginx

```
apt install nginx
```

### 1、配置nginx

```
cd /etc/nginx/conf.d/

vim blog.conf
```

blog.conf 内容如下：

```
server {
    listen    80 default_server;
    listen    [::] default_server;
    server_name    zhangdeyu.vip;#可以写自己的域名
    root    /var/www/hexo;
}
```

检查Nginx语法并重载nginx：

```
nginx -t

nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful

nginx -s reload
```

## 三、解析域名

将域名解析到自己的服务器即可。

![](/img/hexo/7.png)

> 记得把根目录下的 _config.yml 文件中的 repo 改成服务器上的git地址！