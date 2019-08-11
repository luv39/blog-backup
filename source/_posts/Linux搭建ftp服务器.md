---
title: Linux搭建ftp服务器
date: 2019-08-10 18:37:57
tags: linux
---

在使用搭建ftp服务器之前先说一下ftp，sftp，vsftp之间的关系，我之前因为不了解，所以一脸懵逼。ftp和vsftp使用的都是ftp协议，vsftp更加的安全。而sftp使用的是ssh协议，所以它跟其他两个是不一样的，它只需要安装ssh之后就可以使用了。

#### 安装vsftpd

```sh
$ sudo apt install vsftpd
```

#### 创建用户

```sh
$ sudo useradd -m ftpuser
$ sudo passwd ftpuser
```

#### vsftp配置文件


首先使用`cp /etc/vsftpd.conf /etc/vsftpd.conf.copy`备份配置文件，然后使用`vim /etc/vsftpd.conf`打开配置文件，并做以下更改

```
# 需要更改的选项如下



```
