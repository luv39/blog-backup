---
title: 从零开始配置家用nas
date: 2019-08-10 16:15:34
tags: linux
---

我们为什么需要一个家用nas，我主要是用来文件集中存储。我的很多文件都存储在我的nas上，然后我就可以在家里使用任意终端去访问，而不用到处拷贝。

现在最简单的用上nas系统的方法，就是找一台旧电脑，装上黑群晖，黑群晖的系统做得确实很好，开箱即用。但是毕竟是别人公司的系统，直接拿过来用，这种做法并不提倡，也体会不到折腾的乐趣。

我目前的nas使用的是Ubuntu server的系统，系统稳定性应该没有问题，主要实现的功能有：
* 软raid保证文件安全
* samba服务器
* ftp服务器
* git服务器
* 远程下载
* 文件分享
* 发送邮件

#### 系统安装

我的硬件配置选择的是j1900的CPU，最好是用千兆的网卡，然后多搞几块硬盘，系统盘选择固态，数据盘直接机械就好了，我没有选择专用的nas硬盘，直接选择的普通硬盘，因为要做raid，反正已经保证文件安全了，管它硬盘啥时候挂呢，就算用了nas硬盘，还是要组raid，nas硬盘又那么贵，哪个活得长还真说不好。安装系统还是很简单的，直接去官网下载镜像，使用rufus写入到U盘，然后插到机器上从U盘启动就好了，一直下一步，系统就安装好了。

安装完成之后，我们需要将软件源更换为国内源，这样下载软件会快很多。

使用`sudo vim /etc/apt/source.list`命令修改，以下是我提供的阿里源

```
deb http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic main restricted universe multiverse
 
deb http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-security main restricted universe multiverse
deb http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-updates main restricted universe multiverse
   
deb http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-proposed main restricted universe multiverse
    
deb http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
deb-src http://mirrors.aliyun.com/ubuntu/ bionic-backports main restricted universe multiverse
```

然后更新一下系统

```sh
sudo apt update
sudo apt upgrade
```

#### 组软raid

使用
