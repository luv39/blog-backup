---
title: 使用hexo+github搭建个人博客
date: 2019-07-23 22:55:22
tags: hexo
---

## 安装nodejs和git

*   Windows到[官网](http://nodejs.cn/download/)下载安装包
*   linux安装nodejs：

```shell
# linux安装nodejs
curl https://raw.github.com/creationix/nvm/v0.33.11/install.sh | sh
wget -qO- https://raw.github.com/creationix/nvm/v0.33.11/install.sh | sh
nvm install stable

# npm换淘宝源
npm install -g cnpm --registry=https://registry.npm.taobao.org
```

## 安装hexo

```shell
cnpm install -g hexo-cli
```

## 配置hexo

```shell
# 初始化一个目录
hexo init blog
cd blog
cnpm install

# 到hexo主题网站选择一个喜欢的主题，我这里选的是aircloud，把主题clone到themes文件夹
git clone https://github.com/aircloud/hexo-theme-aircloud.git themes/aircloud
```

*   配置_config.yaml可以参考以下设置

```yaml
# Hexo Configuration
## Docs: https://hexo.io/docs/configuration.html
## Source: https://github.com/hexojs/hexo/

# Site
title: AirCloud Blog
subtitle: sometimes code， sometimes design
author: Xiaotao Nie
language: zh
timezone:

# URL
## If your site is put in a subdirectory, set url as 'http://yoursite.com/child' and root as '/child/'
url: http://niexiaotao.cn/
root: /
permalink: :year/:month/:day/:title/
permalink_defaults:

#Custom Setting Start

# Site settings
# 网站综合内容设置：
SEOTitle: AirCloud的博客 | AirCloud's Blog
email: onlythen@yeah.net
description: "聂小涛的博客"
keyword: "腾讯"

# SNS settings
# 一些社交平台地址，支持以下几种：
weibo_username:     3286578617
zhihu_username:     ai-er-lan-xue-da
github_username:    AirCloud
twitter_username:   iconie_alloy
facebook_username:  xiaotao.nie.5
linkedin_username:  小涛-聂-80964aba

# Build settings
anchorjs: true                          # if you want to customize anchor. check out line:181 of `post.html`

sidebar-avatar: img/avatar.jpg      # use absolute URL, seeing it's used in both `/` and `/about/`

# Friends
# 友情链接
friends: [
    {
        title: "10000H",
        href: "https://www.10000h.top"
    },{
        title: "Xiaotao's Page",
        href: "https://niexiaotao.com"
    },{
        title: "It helps SEO",
        href: "#"
    }
]

#comment:
#  type: gitment
#  id: your-id-created-by-https://github.com/settings/applications/new
#  secret: your-secret-created-by-https://github.com/settings/applications/new
#  owner: aircloud
#  repo: hexo-aircloud-blog

comment:
   type: disqus
   script: 'https://airclouds-blog.disqus.com/embed.js'

donate:
  img: img/donate.jpg
  content: 感谢鼓励

# The following content is not recommended to modify
# 搜索数据文件路径设置，不建议改动：
search:
  path: search.json
  field: post

# 文章样式(是否首行缩进)：
post_style:
  indent: default

# Custom Setting End
# Directory
source_dir: source
public_dir: public
tag_dir: tags
archive_dir: archive
category_dir: categories
code_dir: downloads/code
i18n_dir: :lang
skip_render:

# Writing
new_post_name: :title.md # File name of new posts
default_layout: post
titlecase: false # Transform title into titlecase
external_link: true # Open external links in new tab
filename_case: 0
render_drafts: false
post_asset_folder: true
relative_link: false
future: true
highlight:
  enable: true
  line_number: true
  auto_detect: true
  tab_replace:

# Category & Tag
default_category: uncategorized
category_map:
tag_map:

# Date / Time format
## Hexo uses Moment.js to parse and display date
## You can customize the date format as defined in
## http://momentjs.com/docs/#/displaying/format/
date_format: YYYY-MM-DD
time_format: HH:mm:ss

# Pagination
## Set per_page to 0 to disable pagination
per_page: 8
pagination_dir: page

# Extensions
## Plugins: https://hexo.io/plugins/
## Themes: https://hexo.io/themes/
theme: aircloud

# Deployment
## Docs: https://hexo.io/docs/deployment.html
#deploy:
#  type: git
#  repo: your repo url
#  branch: master
# 同时同步到两个仓库,推荐ssh
deploy:
  type: git
  repo:
    github: your github url
    gitee: your gitee url
```

*   搜索功能设置

```shell
# 要用搜索功能需要安装下列插件
npm install hexo-generator-search --save
```

*   标签和关于页面设置

如果是新项目，默认是没有`标签`页面和`关于`页面的，需要在`source`文件夹下建立`tags`文件夹和`about`文件夹。

其中`tags`文件夹中新建`index.md`并写入：

```markdown
---
layout: "tags"
title: "Tags"
---
```

`about`文件夹下`index.md`为一篇支持 markdown 格式的文件，需要在开头添加：

```mark
---
layout: "about"
title: "About"
date: 2016-04-21 04:48:33
comments: true
---
```

*   favicon的配置

项目的 favicon 默认在你的博客根目录的 `/source/img` 下面，在 `/source/img` 下面添加 favicon.ico 即可，不要添加在主题文件夹内。

## 使用hexo

```shell
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

## 设置github

*   注册github账号
*   创建一个username.github.io的仓库
*   创建sshkey

```shell
ssh-keygen -t rsa -C 'your email'
# 创建好后到家目录下的.ssh目录找到id_rsa.pub复制里面的公钥，然后到github里面添加sshkey，就可以不需要密码上传了

```

*   绑定自己的域名

发布好之后就可以通过*https://username.github.io*访问了，但是很多人希望绑定自己的域名，到本地的blog/source文件夹下创建CNAME文件，里面写上需要绑定的域名。发布一下，然后到域名服务商设置CNAME，就可以使用自己的域名访问了。

## 设置gitee

*   注册gitee账号
*   创建一个和自己名字相同的仓库
*   发布后，进入仓库，点击服务-pages就可以设置好博客了

gitee和github有这样几个区别，gitee访问速度快，github比较慢；gitee每次发布都需要到pages页面下更新，github不需要；gitee不能绑定自己的域名，github可以。gitee有一个gitee pages Pro可以解决它的所有问题，但是99/年。

## 备份

为了我们文章的安全，或者为了在多台电脑上写作，我们需要将blog文件夹进行备份或者说同步。这里我使用坚果云，直接将blog文件夹右键用坚果云同步就好了，其中有几个文件夹不需要同步，可以使用坚果云的选择性同步功能，忽略这几个文件夹：.deploy_git, node_modules, public.

当你的文件丢失了，或者在一台新电脑上开始写作时，只需要安装好必要的软件，在备份的文件夹下执行`cnpm install`，就可以正常开始使用了。