---
title: Hexo设置域名
date: 2022-09-20 13:00:36
tags:
  - Hexo
categories:
  - Hexo
keywords:
  - Hexo
swiper_index: 2
swiper_desc: Hexo设置域名
swiper_cover: /img/hexo/hexo.webp
top_img: /img/hexo/hexo.webp
cover: /img/hexo/hexo.webp
---
# Hexo 设置域名

> 前文使用了 Hexo+GitHub搭建了个人博客，使用markdown写博客很方便，展示效果也不错，但是呢GitHub的域名较长，以及访问速度较慢，现在就试着把访问域名替换成自己的域名。

## 一、申请域名

首先得先拥有一个自己的域名，我用的是阿里云里买的一个以.vip为后缀的域名。 https://www.aliyun.com/

## 二、域名 DNS 解析

```
ping 你的用户名.http://github.io
```

将这个作为主机地址，给域名的DNS解析添加记录。

登录阿里云，到域名列表，选择要绑定的域名。

![](/img/hexo/4.png)

进入【解析】中点击【添加记录】，添加两条记录，添加一个 A记录，然后再添加一个 CNAME记录。

![](/img/hexo/5.png)

## 三、创建 CNAME 文件

在根目录下 source 目录下新建一个文件，取名为 CNAME (无后缀)，填入自己的域名即可。

此时在浏览器中输入个人域名就可以看到自己的博客啦。

## 四、Github域名绑定

打开 Github 中 hexo 的地址，然后切换到 Settings 页，设置Custom domain内容为个人域名，Save保存。

![](/img/hexo/6.png)
