+++
author = "jasm"
title = "[hugo blog.1]介绍hugo与简单使用"
date = "2019-03-09"
description = "使用hugo,github page,github actions搭建一个静态博客"
tags = [
  "hugo",
]
+++

# 介绍

## 什么是hugo
[hugo](https://gohugo.io/)是一个静态的网站生成器。如果你不会写代码，但是需要一个经常会被访问，但是改动频率较小的站点（比如blog,产品的宣传页面），就可以使用上hugo了。

## 与jekyll，hexo的区别
类似hugo这样的静态站点生成器，还有[hexo](https://hexo.bootcss.com/)和[jekyll](https://jekyllrb.com/)。
- hexo基于nodejs开发，没具体用过，类似hugo，社区也有不少主题可以选择，使用简单，应也是一个不错的选择；
- jekyll基于ruby开发，一开始我也是准备使用jekyll，但是碰到了一些问题：
    1. 本人日常使用windows，而jekyll只能在linux上运行，遂只能安装在虚拟机里面的centos；
    2. 本人从没使用过ruby，所以一开始在折腾Gem时，属实花了一些时间，踩了不少坑，主要还是因为GFW对ruby及其包的安装源的拦截 :fu: ，导致体验相当差。

*如果你使用的是**windows系统**，**对计算机知识没啥兴趣**，**想快速看到自己的blog**，建议使用hugo。*

# 安装和使用hugo

## windows安装
本人的环境为win10，安装步骤按照[官网教程](https://gohugo.io/getting-started/installing/)来即可，使用跨平台的二进制文件安装，相当简洁（go的优势）。
1. 先创建`C:\Hugo\bin`（用来装hugo程序）和`C:\Hugo\Sites`（用来装你的站点）文件夹；
2. 再将下载的二进制安装包解压到`C:\Hugo\bin`下；
3. 最后需要把`C:\Hugo\bin`文件夹添加到你的环境变量Path中，这样hugo cli就可以在powershell或者cmd中直接使用了。
4. 运行`hugo version`，成功打印出版本号，安装结束。

## 基本使用

### 生成站点
安装好hugo之后，就可以开始构建自己的website了。

```
C:\> cd C:\Hugo\Sites
C:\Hugo\Sites> hugo new site example #新建example站点 
```

运行之后，在Sites文件夹下，就可以看见一个新的文件夹example，这个就是blog的根目录。

```
.
├ archetypes     # content中用hugo new生成的文章的原型
├ config.toml    # 主配置文件，也可以是yml或者json格式，只要叫这个名字即可
├ content        # blog内容文件夹，blog的主要内容都会放在这里
├ data           # 存储hugo在生成网站时使用到的一些配置文件
├ layouts        # 存储用来渲染静态页面的.html模板
├ static         # 静态文件，css，js，图片等
└ themes         # 存储用到的主题

```

### 添加主题
主题就是你的blog的皮肤，通过更换主题，就可以给你的站点更换皮肤。同时，大部分主题提供了一些个性化的配置，可以让你对主题做一定的修改。如果你懂前端代码，也可以直接fork这些主题，自己改代码来实现你想要的效果。

在[这里](https://themes.gohugo.io/)可以找到不少开源的主题。

下面我们使用[ananke主题](https://themes.gohugo.io/themes/gohugo-theme-ananke/)给页面加个皮肤。

```
C:\Hugo\Sites> cd example        #进到example文件夹
C:\Hugo\Sites\example> git init  #将整个example文件夹初始化为一个git仓库
C:\Hugo\Sites\example> git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke  #submodule方式从主题的github仓库中拉取代码到themes/ananke文件夹，也可以直接进到themes文件夹中直接git clone这个仓库
```
执行完之后，在themes文件夹下，就会出现一个ananke文件夹，存放这个主题的静态文件，文件结构如下。

```
.
├ archetypes    
├ assets
├ exampleSite # 示例site的文件，里面会有一些示例的内容，静态文件，以及示例配置
├ i18n
├ images
├ layouts
├ resources
├ static    
├ theme.toml # 主题的配置文件
├ README.md  # 说明文件，一般内部都会告诉使用者如何配置
└ ...
```
主题下载完之后，还需要配置一下，hugo才会开始使用我们的主题。在`C:\Hugo\Sites\example\config.toml`中添加`theme = 'ananke'`即可。

![配置主题](/images/config_theme.png)

### 启动服务
现在即可启动hugo自带的本地服务器来host我们的静态站点了，虽然目前什么内容都没有，但是皮肤还是能看的到的。

```
C:\Hugo\Sites\example> hugo server -D -t ananke
Start building sites … 
hugo v0.101.0-466fa43c16709b4483689930a4f9ac8add5c9f66+extended windows/amd64 BuildDate=2022-06-16T07:09:16Z VendorInfo=gohugoio

                   | EN  
-------------------+-----
  Pages            | 10
  Paginator pages  |  0
  Non-page files   |  0
  Static files     |  1
  Processed images |  0
  Aliases          |  1
  Sitemaps         |  1
  Cleaned          |  0

Built in 85 ms
Watching for changes in C:\Hugo\Sites\example\{archetypes,content,data,layouts,static,themes}Watching for config changes in C:\Hugo\Sites\example\config.toml, C:\Hugo\Sites\example\themes\ananke\config.yaml
Environment: "development"
Serving pages from memory
Running in Fast Render Mode. For full rebuilds on change: hugo server --disableFastRender    
Web Server is available at http://localhost:1313/ (bind address 127.0.0.1)
Press Ctrl+C to stop
```

出现以上信息，说明我们的hugo server已经启动了。

其中，`-D`代表使用buildDraft模式启动，此模式下，hugo server会一直监听文件的变化，并实时刷新到服务器中，方便我们修改文章并查看效果，`-t ananke`表示使用。

现在通过浏览器访问[localhost:1313](http://localhost:1313/)，就能看到我们的站点了，当然现在还没有任何内容。

![没有内容的站点](/images/originsite.png)

### 生成文章
接下来使用hugo cli自动生成一个文章模板，来看看在该主题下文章是啥样子的了。

```
C:\Hugo\Sites\example> hugo new posts/test.md
```

输入命令后，content文件夹下面会出现一个/posts/test.md文件,这就是一篇文章了，文件中内容如下:
 
> \-\-\-
> 
> title: "Test" # 文章标题
> 
> date: 2022-08-08T17:14:38+08:00 # 文章时间
> 
> draft: true # 是否为草稿，在文章正式commit时，这里应该改为false
> 
> \-\-\-

同时，由于我们刚才使用了启动选项`-D`，在我们的网页中，也刷新出了一篇文章,不过点进去是没有任何内容的。

![](/images/with_test_post.png)

现在在test.md下面追加markdown格式文本，就会成为文章内容：

> \-\-\-
> 
> title: "Test" # 文章标题
> 
> date: 2022-08-08T17:14:38+08:00 # 文章时间
> 
> draft: true # 是否为草稿，在文章正式commit时，这里应该改为false
>
> \-\-\-
> 
> \# Title
> 
> lorem ipsum dolor

回到浏览器看一下，内容就刷新出来了，再下载一个markdown编辑器，你就可以开始编辑文章内容了。

![有内容的站点](/images/with_content.png)
