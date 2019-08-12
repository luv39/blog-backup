---
title: linux下使用mdadm组软raid
date: 2019-08-12 23:04:14
tags: linux
---

为了给自己组一台nas，保护里面数据的安全，所以查阅了挺多资料，终于在虚拟机上试验成功了软raid的一些操作，写篇文章记录一下。

#### 创建软raid

我是想创建一个raid5，raid5至少需要3块硬盘，现在主流的nas上应该都有4个硬盘位（除了系统盘之外）。
在创建之前需要将所有的数据盘分区，一个硬盘一个区。

```sh
sudo fdisk /dev/sdb
# Welcome to fdisk (util-linux 2.31.1).
#Changes will remain in memory only, until you decide to write them.
#Be careful before using the write command.
#
#
#Command (m for help): d
#Selected partition 1
#Partition 1 has been deleted.
#
#Command (m for help): n
#Partition type
#p   primary (0 primary, 0 extended, 4 free)
#e   extended (container for logical partitions)
#Select (default p): 
#
#Using default response p.
#Partition number (1-4, default 1): 
#First sector (2048-10485759, default 2048): 
#Last sector, +sectors or +size{K,M,G,T,P} (2048-10485759, default 10485759): 
#
#Created a new partition 1 of type 'Linux' and of size 5 GiB.
#Partition #1 contains a linux_raid_member signature.
#
#Do you want to remove the signature? [Y]es/[N]o: Y
#
#The signature will be removed by a write command.
#
#Command (m for help): w
#The partition table has been altered.
#Syncing disks.

sudo fdisk /dev/sdc
sudo fdisk /dev/sdd
sudo fdisk /dev/sde
```

分好区之后就可以开始创建软raid了

```sh
sudo mdadm -C /dev/md0 -a yes -l 5 -n 4 /dev/sdb /dev/sdc /dev/sdd /dev/sde
```

选项解读：
* -C 创建
* -l 级别
* -n 磁盘个数
* -a {yes|no} 自动为其创建设备文件
* -x 指定空闲盘个数，空闲盘可以在工作盘损坏后自动顶替
注意：创建阵列时，阵列所需磁盘数为-n和-x参数的个数和

创建完成之后我们就可以开始对磁盘进行格式化，并将配置写入文件

```sh
# 格式化
sudo mkfs.ext4 /dev/md0

# 写入配置文件
sudo mdadm -Ds /dev/md0 > mdadm.conf
sudo mv mdadm.conf /etc/
```

我们还可以查看磁盘状态

```sh
sudo mdadm -D /dev/md0
```

![](https://yaozhijin.coding.net/p/img/git/raw/master/2019-08-12-23-41-43.png)

然后我们需要重启，重启完成之后，md0可能会变，我的变成了md127，我也不知道这是为什么,但是我们可以使用`sudo fdisk -l`来进行查看，查看之后就可以使用`mount`命令或者编辑*/etc/fstab*对这个raid后的磁盘进行挂载使用了。

#### 模拟损坏

我在实验之前已经将所有的磁盘都设置为了可以热插拔，所以在虚拟机运行的时候我也可以移除磁盘。现在我们要模拟损坏，然后如何替换新的磁盘上去，我们先在虚拟机上将其中一个数据盘移除。

```sh
sudo mdadm -D /dev/md127
```

![](https://yaozhijin.coding.net/p/img/git/raw/master/2019-08-12-23-43-39.png)

现在我们可以看到Active Devices和Working Devices都已经变成了3，最下面的一个磁盘也已经显示removed。然后我使用虚拟机在原来的位置插入一个新的磁盘，模拟我们替换上新磁盘。

使用`sudo fdisk -l`可以看到，我们的新磁盘已经被认到了，现在要拷贝正常硬盘上的分区信息到新的硬盘。

```sh
sudo sfdisk -d /dev/sdb | sudo sfdisk -f /dev/sde

# 让内核重载分区表
sudo partprobe /dev/sde

# 将新硬盘添加到原有raid中
sudo mdadm /dev/md127 -a /dev/sde1
# 现在再查看raid的状态
sudo mdadm -D /dev/md127
```

![](https://yaozhijin.coding.net/p/img/git/raw/master/2019-08-12-23-52-10.png)

现在我们可以看到新硬盘已经加入raid，并且已经在重建数据了，时间可能比较久，但是不影响系统运行。









































