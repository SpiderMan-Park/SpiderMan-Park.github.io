---
title: Hexo博客搭建教程
date: 2022-09-16 14:12:56
tags:
  - Hexo
categories:
  - Hexo
keywords:
  - Hexo
abbrlink: Hexo博客搭建教程
swiper_index: 3
swiper_desc: Hexo博客搭建教程
swiper_cover: /img/hexo/hexo.webp
top_img: /img/hexo/hexo.webp
cover: /img/hexo/hexo.webp
---
# Hexo + Github 个人博客搭建教程 【整理】

> Hexo是一个基于 node.js的快速生成静态博客的开源框架，支持 Markdown和大多数 Octopress插件，一个命令即可部署到 Github、Gitee等，强大的APi，可无限扩展，拥有数百个主题和插件。

你可以在上面编写文章，做笔记，写日记，码代码。一个属于你的世界！

除此之外Hexo还提供许多主题模版，将其应用到你的博客中，赏心悦目，简直完美！

## 一、环境准备

### 1、安装Node.js

官网下载安装即可: https://nodejs.org/en/download/

Node.js (v12+) 我这里用的是 v14.18.0

### 2、安装Git

Windows：官网下载并安装即可：https://git-scm.com/downloads.

Mac：使用 Homebrew, MacPorts 或者下载 安装程序。

Linux (Fedora, Red Hat, CentOS)：sudo yum install git-core

Linux (Ubuntu, Debian)：sudo apt-get install git-core

## 二、安装Hexo

### 1、安装Hexo
```
npm install -g hexo-cli
```
安装完成后输入 hexo -v 查看hexo版本

### 2、hexo初始化，新建博客根目录
```
hexo init Blog
```

### 3、进入博客根目录，安装依赖
```
cd Blog
npm install
```
可以看到，hexo已经安装完成了。

```
.
├── _config.yml     网站配置信息
├── package.json    依赖包
├── scaffolds       模板文件夹——存放新建文章
├── source          存放用户资源文件夹
|   ├── _drafts
|   └── _posts
└── themes          主题文件夹
```

### 4、启动服务站点
```
hexo g
hexo s
```
![](/img/hexo/1.png)

> 访问 http://localhost:4000/ 到这里 hexo 就搭建好了。可以在本地访问了

## 三、部署Hexo到GitHub

### 1、新建一个Github仓库

仓库名称格式为：你的用户名+.github.io

![](/img/hexo/2.png)

### 2、安装上传插件

```
npm install hexo-deployer-git --save
```

### 3、修改根目录配置文件->指定仓库路径

修改根目录下 _config.yml 文件

```
deploy:
  type: git
  repo: git@101.34.42.124:/var/repo/blog.git  // 你的github仓库路径
  branch: master
```

### 4、推送hexo到Github

```
hexo d
```

### 5、访问hexo

```
https://SpiderMan-Park.github.io/   // 你的github仓库名称
```

## 四、更换主题

hexo自带的主题 https://hexo.io/themes/

1、下载喜欢的主题并解压

2、放到根目录的 themes文件夹下

3、修改根目录下 _config.yml 文件 
```
theme: 主题名称
```

4、重新启动服务即可

我这里是用的是 butterfly 主题，安装及配置方法如下：

在根目录下打开终端后输入

```
git clone -b master https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly
```

修改根目录下的 _config.yml文件，把主题改为 butterfly

```
theme: butterfly
```

然后要安装 pug 以及 stylus 的渲染器
```
npm install hexo-renderer-pug hexo-renderer-stylus --save
```

为了减少升级主题后带来的不便，请使用以下方法：

把 themes 目录下的 _config.yml文件 复制到 hexo 根目录下，重命名为 _config.butterfly.yml

> 注意：不要把 themes 目录下的 _config.yml文件删掉！

关于主题的相关配置 以后只在 _config.butterfly.yml 中配置就好了。

## 五、更新Github仓库

本地调试成功后，就可以更新到github了。

```
1、hexo clean  // 清理静态文件

2、hexo g  // 构建静态文件

3、hexo d  // 上传至仓库
```

## 六、添加博客文章

博客搭建好了，总感觉少点什么，没错，就是少了文章，来试着添加第一篇文章吧！

```
hexo new "我的第一篇文章"
```

文章存放在 /根目录/source/_posts/我的第一篇文章.md

>Markdown 是一种可以使用普通文本编辑器编写的 标记语言，通过简单的 标记语法，它可以使普通文本内容具有一定的格式，基于 Markdown 语法的简洁性，它已经成为目前世界上最流行的用于书写博客的语言。

markdown教程： https://www.runoob.com/markdown/md-tutorial.html

## 至此，Hexo + Github 个人博客搭建就完成了，赶快试试吧，加油，少年！

<!-- ![](/img/3.webp) -->
<img src="/img/common/3.webp" width="200" height="200" div align=center />
