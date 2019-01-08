---
title: Gnome Ubuntu上安装 Anaconda3
date: 2018-04-07 00:46:54
categories: 
    - "ToolBox"
tags: 
    - Gnome Ubuntu
    - Anaconda
---

主要介绍如何在ubuntu上面配置anaconda
<!--more-->


[anaconda下载页面](https://www.anaconda.com/download/#linux)

下载好.sh文件之后

执行这个命令

```shell
# 按照自己的文件名执行
sh Anaconda3-5.3.0-Linux-x86_64.sh
```

然后按照步骤输入'yes'和enter就好了，最后会让选择是否安装VScode,我已经装了就没选

可能遇到的问题：
1. conda命令无法执行
遇到这种情况首先要开个新terminal试试行不行，要是还是不行的话就把anaconda/bin这个路径放到环境变量里面
```shell
export PATH="/home/XXX/anaconda3/bin:$PATH"
```
2. 执行python发现出来的还是Ubuntu自带的python
这个也是环境变量的问题，按照上面这句话添加就好了

这里还有个介绍如何在bashrc里面设置alias来共同使用anaconda和系统自带python的[教程](https://blog.csdn.net/zhangxinyu11021130/article/details/64125058)，但是我感觉直接用conda的虚拟环境会更方便就没搞，有需要的可以一看