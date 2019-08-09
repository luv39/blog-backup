---
title: Linux使用命令行发邮件
date: 2019-08-09 21:08:01
tags: linux
---

虽然在Linux下有很多邮件软件，但是有的时候我们还是需要在命令行下发送邮件，网上的教程也有很多，但是我尝试了很多，大多数都失败了，记录一下我为数不多的成功经历。

#### 设置POP3/SMTP服务

以163邮箱为例，先进入你的163邮箱，在设置下面的POP3/SMTP/IMAP下设置POP3/SMTP服务。
> 开启服务之后，163邮箱会让你设置授权码，一定要记住这个授权码，下面讲到的邮箱配置文件中的密码是这个授权码，而不是你登陆邮箱的密码!

![](https://yaozhijin.coding.net/p/img/git/raw/master/2019-08-09-21-14-59.png)

#### 安装和配置软件

```shell
sudo apt install mutt msmtp
```

`vim ~/.muttrc`输入以下内容

```shell
set sendmail="/usr/bin/msmtp"
set use_from=yes
set realname="无所谓"
set from=tom@163.com
set envelope_from=yes
```

然后新建日志文件`touch ~/.msmtp.log`
新建mstp配置文件`vim ~/.msmtprc`，写入以下内容

```shell
account	default
host	smtp.163.com
user	tom
from	tom@163.com
password	xxxxxxx
auth	login
tls	off
logfile	~/.msmtp.log
```

> user不能随便填写，一定要是邮箱@之前的字符

由于密码是明文，所以修改文件的权限，只允许自己可以访问该文件 `chmod 600 ~/.msmtprc`
配置好之后可以输入`msmtp --host=smtp.163.com --serverinfo`命令，进行测试，输出以下结果

> SMTP server at smtp.163.com (m12-16.163.com [220.181.12.16]), port 25:
>     163.com Anti-spam GT for Coremail System (163com[20141201])
> Capabilities:
>     PIPELINING:
>         Support for command grouping for faster transmission
>     STARTTLS:
>         Support for TLS encryption via the STARTTLS command
>     AUTH:
>         Supported authentication methods:
>         PLAIN LOGIN 
> This server might advertise more or other capabilities when TLS is active.

#### 使用软件

然后就可以开始使用命令行发送邮件了。

```shell
echo "hello world" | mutt -s "title" 目标邮箱
mutt -s "title" -a 附件 -- 目标邮箱 < 邮件正文
```