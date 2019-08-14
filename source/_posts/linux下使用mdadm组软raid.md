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

#### 模拟数据盘损坏

我在实验之前已经将所有的磁盘都设置为了可以热插拔，所以在虚拟机运行的时候我也可以移除磁盘。现在我们要模拟损坏，然后如何替换新的磁盘上去，我们先在虚拟机上将其中一个数据盘移除。

```sh
sudo mdadm -D /dev/md127
```

![](https://yaozhijin.coding.net/p/img/git/raw/master/2019-08-12-23-43-39.png)

现在我们可以看到Active Devices和Working Devices都已经变成了3，最下面的一个磁盘也已经显示removed。然后我使用虚拟机在原来的位置插入一个新的磁盘，模拟我们替换上新磁盘。

使用`sudo fdisk -l`可以看到，我们的新磁盘已经被认到了，现在要对这个新硬盘进行分区，分区时需要使用**t**将分区类型选择为fd

```sh
# 将新硬盘添加到原有raid中
sudo mdadm /dev/md127 -a /dev/sde1
# 现在再查看raid的状态
sudo mdadm -D /dev/md127
```

![](https://yaozhijin.coding.net/p/img/git/raw/master/2019-08-12-23-52-10.png)

现在我们可以看到新硬盘已经加入raid，并且已经在重建数据了，时间可能比较久，但是不影响系统运行。

#### 模拟系统盘损坏

我们在使用过程中，不光会出现数据盘损坏的情况，也有可能出现系统盘损坏的情况，当系统盘损坏的时候，我们如何让一个新系统识别已有的raid组合。

我在虚拟机上重新安装了系统开机后，发现系统已经直接认到了raid盘，也就是说什么都不用做。

```sh
# 我们主动解除raid组合
sudo mdadm -S /dev/md0
```

当我们主动解除raid盘之后，系统就已经不能认到raid了，这个时候，我们可以使用以下命令再次识别raid,也可以直接将之前的`mdadm.conf`直接复制到*/etc/*目录，重启之后，他就会自动帮我们识别好raid了。

```sh
sudo mdadm -A /dev/md0 /dev/sdb1 /dev/sdc1 /dev/sdd1 /dev/sde1
```

#### 损坏通知

我们不可能每天都去查看硬盘有没有损坏，这样非常麻烦，所以我自己写了一个脚本，实现监控硬盘的功能，当它检测到硬盘损坏时，就会发一封邮件通知你。首先要让自己的nas拥有发邮件的功能，邮件的配置请参考我的[这篇博客](https://www.yaozhijin.top/Linux%E4%BD%BF%E7%94%A8%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%8F%91%E9%82%AE%E4%BB%B6.html)

```python
#! /usr/bin/python3

import os
import time

while 1:
	result = os.popen('mdadm -D /dev/md0')
	res = result.read()
	active = 0

	for line in res.splitlines():
		if 'Active Devices : 4' in line:
			active = 1
			break
		else:
			continue
		active = 0

	if active == 1:
		time.sleep(600)
	else:
		os.system("echo '你的raid可能出现了一些问题，请查看一下' | mutt -s 'raid问题报告' pi_sender@163.com")
		time.sleep(86400)
```

最后在终端执行`sudo ./raid-monitor.py`就好了

参考链接：http://www.toxingwang.com/linux-unix/linux-basic/1048.html
