---
title: Linux搭建samba服务器
date: 2019-08-10 18:38:13
tags: linux
---

#### 下载Samba

```shell
sudo apt install samba
```

#### 账户创建，用户组创建，相关目录创建，目录的属主，属组更改

```shell
# 创建用户组
$ sudo groupadd ceshi
# 创建用户并加入用户组
$ sudo useradd -g ceshi -s /sbin/nologin ceshiuser
# 创建samba用户并给每个用户设置密码，要在系统创建完后才可以创建samba用户 (smbpasswd -a $username命令是用于创建用户和更改密码的命令)
$ smbpasswd -a ceshiuser
New SMB password:
Retype new SMB password:
Added user chanpinuser.
# 创建目录，更改属主
$ mkdir /share
$ chown -R ceshiuser:ceshi /share
# 更改配置文件，实现权限分配
$ vim /etc/samba/smb.conf
```

全局设置保持默认就行，如果不想让别人看到home目录就删掉Share Definitions的home目录，然后参考以下设置Share Definitions

```
# 共享目录名称
[share]
   # 共享目录描述
   comment = share folder
   # 共享目录路径
   path = /share
   # 用来指定该共享是否可以浏览
   browseable = yes
   # 用来指定该共享资源是否可用
   available = yes
   # 是否允许写入
   writable = yes
   # Samba用户在所在目录创建文件的权限
   create mask = 0770
   # Samba用户在所在目录创建文件夹的权限
   directory mask = 0770
   # 允许访问的用户
   valid users = share
```