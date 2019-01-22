---
title: Gnome Ubuntu16安装Nvidia显卡396驱动，CUDA9.2以及cudnn9.2
date: 2018-04-06 00:58:51
categories: 
    - "ToolBox"
tags: 
    - Gnome Ubuntu
    - "deeplearning"
---
深度学习环境配置，安装Nvidia显卡驱动，CUDA以及cudnn
<!--more-->

OS:ubuntu 16.04;
driver: nvidia 396;
CUDA: 9.2
cudnn: 9.2

## 卸载原有Nvidia驱动
```
# 卸载原有Nvidia驱动
sudo apt-get remove --purge nvidia-*
```

## 安装驱动
apt-get安装而非下载安装，有一些博客说下载安装总有问题
然而，我两种方法都没装上（见处理 nouveau）
```
# apt-get安装
sudo apt-get install nvidia-384 （之前装CUDA9.0的时候装384）
sudo apt install nvidia-396 （现在CUDA9.2需要396）


# 获取Kernel source（非常重要）：
sudo apt-get install linux-source
sudo apt-get install linux-headers-4.15.0-24-generic
# linux-headers-4.15.0-24-generic　具体版本由第一步获取

# 下载安装
sudo chmod a+x NVIDIA-Linux-x86_64-384.90.run //获取权限
sudo ./NVIDIA-Linux-x86_64-384.90.run -no-x-check -no-nouveau-check -no-opengl-files //安装驱动

```
## 如果显卡驱动装不上，先处理 nouveau
```
lsmod | grep nouveau
```
如果有输出则代表nouveau正在加载。则需要禁用nouveau

/etc/modprobe.d中创建文件blacklist-nouveau.conf，再用gedit打开
```
cd /etc/modprobe.d
/etc/modprobe.d$ sudo touch blacklist-nouveau.conf
sudo gedit blacklist-nouveau.conf
```
在文件中输入以下内容并保存：
```
blacklist nouveau  
options nouveau modeset=0 
```
之后更新
```
sudo update-initramfs -u
```
再次查看
```
lsmod | grep nouveau
```
这种方式也可能不能彻底禁用nouveau，在此基础上可以移除以下文件：nouveau.ko；nouveau.ko.org
```
cd /lib/modules/4.4.0-83-generic/kernel/drivers/gpu/drm/nouveau 
sudo rm -rf nouveau.ko 
sudo rm -rf nouveau.ko.org
```
再更新
```
sudo update-initramfs -u
```
时重启，再用终端检测一下
```
lsmod | grep nouveau
```
没有输出即为禁用成功。

**其实上面这些操作我做完之后，重启还是黑屏了；后来重新在命令行中删除Nvidia的东西再装驱动再重启才好的**

nvidia驱动安装成功界面
![nvidia-smi](Ubuntu16_CUDA9_Tensorflow/nvidia-smi.png)

# CUDA9.2安装

[CUDA](https://developer.nvidia.com/cuda-92-download-archive?target_os=Linux&target_arch=ppc64le&target_distro=Ubuntu&target_version=1604&target_type=deblocal)

下载文件

执行
```
Installation Instructions:
`sudo dpkg -i cuda-repo-ubuntu1604-9-2-local_9.2.148-1_ppc64el.deb`
`sudo apt-key add /var/cuda-repo-<version>/7fa2af80.pub`
`sudo apt-get update`
`sudo apt-get install cuda`
```
看网上有人用deb安装会有问题，但是我这么装没事儿呀 

检测CUDA是否安装好
```
cd  /usr/local/cuda-9.2/samples/1_Utilities/deviceQuery

sudo make

./deviceQuery
```
如果显示的是关于GPU的信息，则说明安装成功了。

别人用runfile安装的时候 有几个步骤需要选择，其中有个选择是否安装nvidia驱动的要选N！我之前就在这里装错了，超烦

还有有一些教程说要在~/.bashrc里面增加环境变量
```
export PATH=/usr/local/cuda-9.2/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-9.2/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}

在终端执行命令
source ~/.bashrc
```



# cuDNN安装

[cudnn](https://developer.nvidia.com/rdp/cudnn-archive#a-collapse721-92)

```
tar -zxvf cudnn-9.2-linux-x64-v7.2.1.38.tgz

sudo cp cuda/include/cudnn.h /usr/local/cuda/include
sudo cp lib64/libcudnn* /usr/local/cuda/lib64
sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*

```

然后可以执行
```
nvcc -V
```
查看是否成功

# caffe
# pytorch
# TF



参考文章：
[Ubuntu16.04 安装NVIDIA驱动常见Error的解决方案](https://blog.csdn.net/ksws0292756/article/details/79160742)

[安装参考](https://www.cnblogs.com/pertor/p/8733010.html)