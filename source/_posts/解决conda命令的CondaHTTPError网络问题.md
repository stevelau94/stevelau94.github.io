---
title: 解决conda命令的CondaHTTPError网络问题
date: 2018-04-07 01:00:55
categories: 
    - "ToolBox"
tags: 
    - Gnome Ubuntu
    - Anaconda
---

通过修改清华源和配置文件以及清楚代理的影响解决conda命令的CondaHTTPError网络问题
<!--more-->


安装好anaconda之后，想要使用conda命令。

但是无论是conda install还是conda create命令都显示如下情况

```
Solving environment: done

CondaHTTPError: HTTP 000 CONNECTION FAILED for url <https://repo.anaconda.com/pkgs/r/linux-64/repodata.json.bz2>
Elapsed: -

An HTTP error occurred when trying to retrieve this URL.
HTTP errors are often intermittent, and a simple retry will get you on your way.

If your current network has https://www.anaconda.com blocked, please file
a support request with your network engineering team.

ProxyError(MaxRetryError("HTTPSConnectionPool(host='repo.anaconda.com', port=443): Max retries exceeded with url: /pkgs/r/linux-64/repodata.json.bz2 (Caused by ProxyError('Cannot connect to proxy.', NewConnectionError('<urllib3.connection.VerifiedHTTPSConnection object at 0x7f7dc344dba8>: Failed to establish a new connection: [Errno 111] Connection refused')))"))

```

可见是CondaHTTPError  

这种情况在google之后比较好处理：

执行这几条命令，将conda的安装源换成清华的源：
```
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/main/
conda config --set show_channel_urls yes
```
[清华源](https://mirrors.tuna.tsinghua.edu.cn/help/anaconda/)

如果再次执行conda命令，还是有问题

那就在~/.condrac 中做修改(将default那一行删除)
```
channels:
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/bioconda/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/msys2/
  - https://mirrors.tuna.tsinghua.edu.cn/anaconda/cloud/conda-forge/
show_channel_urls: true
```

这样，一般情况下都应该可以正常执行conda命令了

但是，我进行了如上修改还是不行
```
Solving environment: done

CondaHTTPError: HTTP 000 CONNECTION FAILED for url <https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/noarch/repodata.json>
Elapsed: -

An HTTP error occurred when trying to retrieve this URL.
HTTP errors are often intermittent, and a simple retry will get you on your way.
ProxyError(MaxRetryError("HTTPSConnectionPool(host='mirrors.tuna.tsinghua.edu.cn', port=443): Max retries exceeded with url: /anaconda/pkgs/free/noarch/repodata.json (Caused by ProxyError('Cannot connect to proxy.', NewConnectionError('<urllib3.connection.VerifiedHTTPSConnection object at 0x7fb394511c88>: Failed to establish a new connection: [Errno 111] Connection refused')))"))

```

想起了之前自己设置过proxy，隐隐约约感觉在影响着这里

查到了一些shell命令
```
#查看本地端口的使用情况
sudo netstat -ntpl
```

```
# grep proxy
export | grep -i prox
```
返回了这些
```
declare -x HTTPS_PROXY="http://127.0.0.1:35839/"
declare -x HTTP_PROXY="http://127.0.0.1:35839/"
declare -x NO_PROXY="localhost,127.0.0.0/8,::1"
declare -x http_proxy="http://127.0.0.1:35839/"
declare -x https_proxy="http://127.0.0.1:35839/"
declare -x no_proxy="localhost,127.0.0.0/8,::1"
```

说明确实proxy在影响着conda

然后将这些proxy杀掉
```
unset HTTPS_PROXY
```

再进行conda create就可以正常使用了





