---
title: Linux mint创建应用程序菜单
date: 2019-08-03 22:03:07
tags: linux
---

# Linux mint创建应用程序菜单

我们有很多软件可以在终端打开，但是在应用程序里面找不到，这样有时候会很不方便，所以我们需要自己创建应用程序菜单。

Linux mint下在`～/.local/share/applications`目录下创建***.desktop文件，内容如下：

```shell
[Desktop Entry]
Encoding=UTF-8
Type=Application
Terminal=false
Exec=应用程序所在路径/android-studio/bin/studio.sh
Categories=Application;Development;
Name=应用程序名称
Comment="Android Development IDE(这个其实可以省略)"
Icon=应用程序图标所在路径/android-studio/bin/idea.png
```

