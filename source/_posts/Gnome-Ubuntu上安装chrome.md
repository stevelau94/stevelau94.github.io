---
title: Gnome Ubuntu上安装 chrome
date: 2018-07-03 20:46:38
categories: "Gnome Ubuntu"
tags: 
    - ubuntu
---
这篇文章介绍如何在Gnome Ubuntu 16.04上安装chrome
<!--more-->
### 获取 source list
```
sudo wget http://www.linuxidc.com/files/repo/google-chrome.list -P /etc/apt/sources.list.d/

wget -q -O - https://dl.google.com/linux/linux_signing_key.pub  | sudo apt-key add -

```
### 然后直接安装就行
```
sudo apt-get update

sudo apt-get install google-chrome-stable

```

[ref](https://blog.csdn.net/qq551551/article/details/78885704)