---
title: Gnome Ubuntu上安装 git
date: 2018-04-03 20:32:41
categories: 
    - "ToolBox"
tags: 
    - Gnome Ubuntu
    - Git
---
这篇文章介绍如何在Gnome Ubuntu 16.04上安装git
<!--more-->
### 安装 git
```

sudo add-apt-repository ppa:git-core/ppa

sudo apt-get update

sudo apt-get install git 

```

验证是否安装成功
```
git --version
```

### Git config 设置
```
git config --global user.name 'username'

git config --global user.email 'username@gmail.com'

```

### Git SSH Key 设置
```
# generate the sshkey
ssh-keygen -t rsa -C 'username@gmail.com'

# 获取 public key
# paste it to github
cat ~/.ssh/id_rsa.pub 

# 这个是 priviate key
cat ~/.ssh/id_rsa
```

### 测试 ssh key 是否设置成功
```
ssh -T git@github.com
```
### 如果成功连接上github会返回
```
'Hi username! You've successfully authenticated'
```


[ref](https://www.linuxidc.com/Linux/2016-11/136769.htm)
[ref](https://www.cnblogs.com/visugar/p/6821777.html)