---
title: Gnome Ubuntu上安装 搜狗拼音
date: 2018-07-03 20:49:56
thumbnail: https://www.hdwallpapers.net/previews/hot-air-balloon-over-the-mountain-987.jpg
categories: "Gnome Ubuntu"
tags: 
    - ubuntu
---
这篇文章介绍如何在Gnome Ubuntu 16.04上安装搜狗拼音
<!--more-->

安装sougou pinyin有点烦，因为网上的Ubuntu16.04安装sougou的教程都是unity图形界面的，而我是用的是gnome Ubuntu 16.04 所以安装搜狗不大一样

我先在搜狗官网下载了deb包
然后 
```
sudo dpkg -i sogoupinyin_2.2.0.0108_amd64.deb 
```

但是好像有error 没有装上

然后我执行了
```
sudo apt-get install -f 
```

显示安装了一堆东西，其中好像有fctix

然后重启 我发现左下角就有fctix了（微懵）

然后我在fctix的设置里面选择的sougou pinyin安装的 

再重启就可以使用搜狗了

[ref](https://www.cnblogs.com/darklights/p/7722861.html)