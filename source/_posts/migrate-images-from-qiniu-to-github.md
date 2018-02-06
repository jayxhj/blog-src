---
title: 把图床从七牛迁移到 GitHub
date: 2018-02-06T19:10:31.000Z
tags:
  - 迁移
  - blog
categories: blog
---

事情是这样，国内的域名是需要备案的，备案其实是备案服务器，这样通信管理局就能知道你的网站在哪里托管，在哪台服务器。这里有详细介绍为何要备案 ~~[为什么要备案](https://help.aliyun.com/knowledge_detail/36907.html?spm=a2c4g.11174283.6.539.HMkMNn)~~ 这实在是个神奇的规定，哪些网站能访问，居然是别人替你做决定。

问题就来了，没备案，不仅解析会有问题，连 CDN 都是用不了的，人在屋檐下不得不低头？所以就有了这篇，如何将七牛的图片迁移至 GitHub。

共有以下几个步骤：

1. 获取文章中的图片 url
2. 将 CDN 上的图片下载下来
3. 移动至 Hexo source/images 目录
4. 推送至 GitHub

问题有几个（方案放括号里）：

1. 博客的链接需要更改，如 img.jayxhj.com 需要改为 blog.jayxhj.com/images （sed替换）
2. 图片 url 在 Markdown 文件内 （grep 就好）
3. 由于域名下线，那么直接按原有的 url 访问获取不到对应图片的 （将域名替换为七牛分配的 CDN 加速域名）
4. 显然手动一个个下载图片是不好的 （awk 循环顺序下载）

以上问题，用命令解释就是

```bash
grep -ohE "\!\[.*\]\(.*\)" ./source/_posts/* | grep -oE "blog.jayxhj.com/images[a-zA-Z0-9./?=_-]*" | sed 's/blog.jayxhj.com\/images/oiiz5quoo.qnssl.com/g' | awk '{sysetm("curl -O" $0)}'
```

下面一步步说明。

## 获取文章中的图片 url

由于 Hexo 的文章源文档都是 Markdown 文档，因此需要将所有的图片地址获取出来，命令如下：

```bash
grep -ohE "\!\[.*\]\(.*\)" ./source/_posts/* | grep -oE "blog.jayxhj.com/images[a-zA-Z0-9./?=_-]*"
```

第一件事，是匹配 MarkDown 中的图片 url，`grep -ohE "\!\[.*\]\(.*\)" ./source/_posts/*` 命令的作用是匹配 `![]()` 这样的 url，再提取链接。

由于我们的目的是获取对应 url，现在是知道了域名的链接及前缀 **blog.jayxhj.com/images**，那么做以下事情，使用正则表达式匹配 url，对应 `-E "blog.jayxhj.com/images[a-zA-Z0-9./?=_-]*"`，仅截取匹配到的部分，那就是-o，表示 --only-matching ，-h 表示 --no-filename ，这样获取从多个文件获取时，就不会有文件名了。

## 将 CDN 上的图片下载下来

所有文章的 url 获取到了，但是这些图片没法访问，因为域名未备案，七牛会将资源屏蔽，但是通过七牛提供的 CDN 域名是可以下载的，我的是 **oiiz5quoo.qnssl.com** 。

前面获取到了，那么现在只需要替换域名，curl 下载即可。命令为 `sed 's/blog.jayxhj.com\/images/oiiz5quoo.qnssl.com/g' | awk '{sysetm("curl -O" $0)}`

此处有提醒：如果图片存放在七牛的文件夹里，那么应该按照在七牛定义的分割符来拼接出目录名，再递归创建目录，mkdir -p 即可。

## 将文章内的图片域名替换

```bash
gsed -i 's/http:\/\/img.jayxhj.com/\/\/blog.jayxhj.com\/images/g' ./source/_posts/*
```

希望以后备案这个词可以消失 😊 。
