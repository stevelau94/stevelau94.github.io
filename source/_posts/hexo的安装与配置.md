---
title: 在GnomeUbuntu上安装hexo,配置及部署至gitpage
date: 2018-04-02 21:49:15
categories: 
    - "ToolBox"
tags: 
    - Gnome Ubuntu
    - Hexo
---
这算是自己第一个blog，其实以前也有写一些但是没有整理过    
因为懒吧  
终于不懒了，建个hexo也算是给自己一个记录的地方  
把这次搭建的过程记录了下来，希望可以帮到读者  
<!--more-->
其实整个下来的步骤超级简单，无脑复制粘贴应该就能成  

我使用的环境是：Ubuntu+git+github  
之前我也在Windows10上面弄过，步骤基本一样  

有一些前面需要注意的事：
1. github的SSHKey需要配置好
2. 本地的git config需要配置好


### 安装 nvm
```
# 两种方法装nvm

# cURL:

curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash

# Wget:

wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
```

检查下面两句是否加入环境变量 ~/.bashrc
```
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```

记得环境变量修改之后需要激活一下
```
source ~/.bashrc
```

检查nvm是否安装成功，如果没有安装成功会有提醒
```
command -v nvm
```


### 安装 node
```
nvm install stable
```


**npm 安装 超级 慢！**
#### 更改npm源（这步很重要）
```
# 临时使用
npm --registry https://registry.npm.taobao.org install express

# 持久使用
npm config set registry https://registry.npm.taobao.org
```

### 安装 hexo

```
npm install -g hexo-cli
```


### 初探 hexo

新建个blog文件夹   
在里面执行
```
hexo init
```
就可以看到hexo在里面生成了很多博客文件，具体都是什么用途以后再讨论

执行
```
hexo clean
hexo g
hexo s
```
就可以在本地浏览器打开 http://localhost:4000/ 页面了

是不是很方便呢！


### 部署 hexo 到 github

大概的步骤：  
1. 新建github同名项目
2. 安装hexo-deployer-git
3. 执行本地命令

想在github上面部署，需要新建个以这个格式命名的项目

> username.github.io


hexo 的部署功能需要安装，**这步如果前面没有改成国内源会一直装不上**
```
npm install hexo-deployer-git --save
```

安装好之后 配置hexo博客根目录下的_config.yml文件

```
# Deployment
## Docs: http://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: git@github.com:username/username.github.io.git
  branch: master
```

记住这里要用git@github这种ssh格式（忘记从哪里看到说是hexo新版的要求）

执行
```
hexo clean
hexo g
hexo d
```
就完成部署了   

然后再username.github.io就可以看到自己的hexo blog了

如果本地显示  git done   
但是打开页面还是404的话    
可能是浏览器缓存的问题  清一下缓存试试

