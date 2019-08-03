---
title: Ubuntu18+i3配置
date: 2019-07-23 20:00:49
tags: linux
---



## 安装i3

```shell
sudo apt install i3
```

## 配置i3

登出账户，选择i3桌面进入

第一次进入时会让你设置mod键

按mod+enter打开终端

```shell
# compton用于设置透明，feh设置桌面壁纸，
sudo apt install compton feh

# Albert打开应用
sudo add-apt-repository ppa:noobslab/macbuntu
sudo apt update
sudo apt intall albert

# xfce4-volumed可以使用键盘调节音量
sudo apt install xfce4-volumed

# xfce4-power-manager可以使用键盘fn组合键调节屏幕亮度和键盘背光
sudo apt install xfce4-power-manager
# 如果没有生效，可以打开xfce4-power-manager-config，在里面有个绑定按键的选项，打钩就好了

# touchpad-indicator可以简单地设置触控板
sudo add-apt-repository ppa:atareao/atareao
sudo apt update
sudo apt install touchpad-indicator
```

打开配置文件*~/.config/i3/config*

```shell
# 设置软件自动启动
# 每次按mod+shift+r刷新就会生效
exec_always albert
exec_always compton
exec_always --no-startup-id feh --bg-scale /home/tom/Pictures/wallpaper.jpg
# 只有每次启动桌面的时候才会开启
exec xfce4-volumed
exec xfce4-power-manager
exec touchpad-indicator

# 如果里面有不习惯的快捷键，可以自己设置快捷键
# 设置自己的快捷键打开软件
bandsym $mod+g exec google-chrome

# 配置完成后重启，使所有设置生效
```

