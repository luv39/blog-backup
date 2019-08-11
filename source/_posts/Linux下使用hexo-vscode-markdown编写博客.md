---
title: Linux下使用hexo+vscode+markdown编写博客
date: 2019-08-04 18:46:34
tags: linux
---

### hexo

#### 安装nodejs和git

*   Windows到[官网](http://nodejs.cn/download/)下载安装包
*   linux安装nodejs：

```sh
# linux安装nodejs
curl -sL https://deb.nodesource.com/setup_10.x | sudo -E bash -
sudo apt install nodejs

# npm换淘宝源
sudo npm install -g cnpm --registry=https://registry.npm.taobao.org
```

#### 安装hexo

```sh
sudo cnpm install -g hexo-cli
```

#### 配置hexo

```sh
# 初始化一个目录
hexo init blog
cd blog
cnpm install

# 到hexo主题网站选择一个喜欢的主题，我这里选的是aircloud，把主题clone到themes文件夹
git clone https://github.com/aircloud/hexo-theme-aircloud.git themes/aircloud
```

*   配置_config.yaml可以参考以下设置

[官方Demo](https://github.com/aircloud/hexo-aircloud-blog/blob/master/_config.yml)

在文件最后设置需要同步的仓库，可以设置多个：
```yml
deploy:
    type: git
    repo:
        github: your github repo url
        gitee: your gitee repo url
        coding: your conding repo url
```

* 其他功能的配置

请参考[官方文档](https://github.com/aircloud/hexo-theme-aircloud)

#### 使用hexo

```sh
# 新建文章
hexo new filename	# 它会在source/_posts文件夹创建一个新的markdown文件，在这里面写文章就好了

# 清空缓存，每次生成前最好执行一下
hexo clean

# 生成静态文件
hexo g

# 启动本地服务器，每次发布前可以在本地看一下效果
hexo s

# 发布到远程仓库前，需要安装以下插件
cnpm install hexo-deployer-git --save

# 发布到远程仓库
hexo d
```

#### 设置github

*   注册github账号
*   创建一个username.github.io的仓库
*   创建sshkey

```sh
ssh-keygen -t rsa -C 'your email'
# 创建好后到家目录下的.ssh目录找到id_rsa.pub复制里面的公钥，然后到github里面添加sshkey，就可以不需要密码上传了
```

*   在github设置里添加自己的sshkey
*   绑定自己的域名

发布好之后就可以通过*https://username.github.io*访问了，但是很多人希望绑定自己的域名，到本地的blog/source文件夹下创建CNAME文件，里面写上需要绑定的域名。发布一下，然后到域名服务商设置CNAME，就可以使用自己的域名访问了。

#### 设置gitee

*   注册gitee账号
*   创建一个和自己名字相同的仓库
*   发布后，进入仓库，点击服务-pages就可以设置好博客了
* gitee和github有这样几个区别
  * gitee访问速度快，github比较慢
  * gitee每次发布都需要到pages页面下更新，github不需要
  * gitee不能绑定自己的域名，github可以
* gitee有一个gitee pages Pro可以解决它的所有问题，但是99/年

#### 设置coding

最近发现了一个代码托管网站[coding](https://coding.net/)，它用来做博客比上面两个都要合适，因为它有自动发布，在国内访问速度比较快，可以绑定自己的域名（无需备案），并且完全免费。

用法和上面两个完全相同，创建仓库后进入仓库，点击持续部署-静态网站，就可以设置好博客了。

> 这三个代码托管网站，我推荐在github和coding上都部署一个，防止其中一个突然挂了，gitee那个太麻烦了，就不考虑了。

#### 博客文件的备份

我们要居安思危，为了防止哪天我们的电脑直接挂了，然后带走我们所有的博客，所以我们要备份我们的hexo文件夹。上传到远程仓库的那些只是渲染完成之后的网页，并不是我们编写的markdown文件。如果你使用的是Windows系统，我推荐使用坚果云进行同步，直接把整个文件夹同步就好了。当然，有些文件夹是不用同步的，坚果云可以设置忽略文件夹，不用同步的文件夹有public和.deploy_git. 但是我使用的是Linux，而Linux版的坚果云不可以设置忽略文件夹，所以我就直接使用git进行备份了。

### markdown

markdown是个好东西，Linux上优秀的markdown编辑器也不少，比如typora就有Linux版本的，但是在我的电脑上好像有点bug，无法右键打开文件，所以我就选择了vscode。
我在vscode上安装的插件主要有:
* Markdown All in One(集成了markdown的一些功能)
* Markdown Preview Enhanced(预览markdown文件)
* Paste Image(自定义粘贴图片的链接的)
* One Dark Pro(主题，好看)
* Typewriter Scroll Mode(当你打字的时候，自动向上滚动，保证你的光标在屏幕正中央)
* Power Mode(打字的时候有特效,好看)

![](https://yaozhijin.coding.net/p/img/git/raw/master/gif/demo-presets-particles.gif)

编写markdown文件最麻烦的还是图片的插入，最好的解决方案就是图床，Windows下有一款很好用的图床软件叫Picgo，在Linux下也有，但是里面提供的图床真正稳定免费的只有github图床，但是github图床访问又太慢了，所以我就放弃了使用Picgo。

其实只要是可以使用git的代码托管网站都可以作为我们的图床，它们的图片产生的外链前面都是一样的，只有文件名不同，我们只需要使用*Paste Image*插件将图片保存到电脑里的同一个目录，然后设置好图片链接的格式，最后将目录里的图片全都push到远程仓库就可以访问了。

这样的好处是本地也会保存有一份图片副本,如果你的远程仓库挂了,图片也不会丢失,你可以寻找其他的替代方案,到时候批量把链接改掉就可以直接访问了.

#### Paste Image 设置

先说Paste Image的设置，点击vscode左下角的小齿轮,选择设置-扩展-Paste Image Configuration, 需要更改的只有两项:
* Paste Image: Insert Pattern(粘贴的链接格式,your_url的获取方法见本地仓库设置)
    \${imageSyntaxPrefix}your_url\${imageFileName}\${imageSyntaxSuffix}
* Paste Image: Path(图片在本地保存的绝对路径)

这样你每次复制图片之后,使用ctrl+alt+v粘贴到vscode的时候,图片将自动保存到你设置的文件夹里,链接格式也已经帮你转换好了.

#### git设置

这时候图片还在本地文件夹保存着,但是链接是远程仓库的链接,还是访问不了图片的,现在就是要把图片文件夹同步到远程仓库.

这里远程仓库我使用的是国内的coding,本来想用gitee的,但是gitee的访问好像有点问题,所以只好用coding了.我在使用gitee的时候,本地的预览无法显示出图片,但是直接将链接粘贴到浏览器是可以访问的,将文章发布到博客之后,我在gitee和github都部署了博客,结果gitee的无法显示出图片,github的可以,Interesting!

##### 远程仓库设置

1. 在coding注册登录
2. 在coding创建一个公开的仓库
3. 将sshkey公钥添加到coding设置里
4. 复制仓库的链接备用

##### 本地仓库设置

1. 到上面Paste Image里面设置的文件夹路径下打开终端,执行以下命令.

```sh
# 初始化仓库
git init

# 与远程仓库关联
git remote add origin your-repo-url(这就是上面复制的仓库链接)

# 拉取远程仓库的内容
git pull origin master

# 在文件夹放一个测试图片,将本地仓库的内容推送到远程仓库并绑定
git push --set-upstream origin master

# 以后每次上传时候的三部曲
git add .
git commit -m 'update'
git push
```

现在图片已经上传到远程仓库了,现在到远程仓库点击那张图片,点击右上角的原始数据,这个时候浏览器上地址栏的链接就是这张图片的外链了,这个链接除了文件名之外的部分就是上面要设置的*Paste Image: Insert Pattern(粘贴的链接格式)*中的*your_url*的部分.

##### 自动上传脚本

到现在为止,在markdown中添加图片的步骤就是:
1. 复制图片
2. 使用ctrl+alt+v粘贴链接到vscode
3. 使用上传三部曲,上传图片

但是每次都要打开终端,进入那个目录,执行三条命令进行上传,实在是太麻烦了,所以我自己又写了一个脚本,自动上传图片.我使用的是Python,需要依赖gitpython库.

```python
#! /usr/bin/python3

import git

pushPath = "你的图片文件夹的绝对路径"

repo = git.Repo(pushPath)
origin = repo.remote(name="origin")

def autoPush():
    repo.git.add(".")
    repo.git.commit(m="update")
    origin.push()

if __name__ == "__main__":
    autoPush()
```

授予此脚本执行权限,然后把它绑定到一个快捷键,然后每次就可以直接按一下快捷键自动上传了.
