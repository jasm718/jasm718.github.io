+++
author = "jasm"
title = "<hugo blog.2>使用github pages展示你的博客"
date = "2019-03-15"
description = "使用hugo,github page,github actions搭建一个静态博客"
tags = [
  "hugo",
]
+++

# 为什么使用github pages
在使用hugo生成静态站点后，为了让其他人也能够看到你的站点，需要把它放到互联网上。

在没有github pages或其他类似的静态站点托管服务之前，通常的做法是：
- 拥有一台有公网ip的服务器（或云服务器），并将hugo源码都放到该服务器；
- 使用nginx（或其他http服务器）提供对静态文件的访问；
- 如果想要方便的被别人访问到，还需要购买域名，要不然别人就需要通过ip+端口号方式访问你的博客了。

综上，想要搭建一个简单的静态内容博客，至少需要一个服务器，一个域名。显然配置是过剩的。

所以，就有了github pages这个替代方案。github pages可以通过我们上传的代码，自动生成一个静态站点，再配合上一些CI/CD脚本，就可以在每次提交代码时候，让github为我们自动更新静态博客了。

通过github pages去托管静态博客，可以：
- 不再需要服务器，github pages服务会为我们托管内容；
- 不再需要域名，使用了github pages服务之后，github提供了一个xxx.github.io供访问博客；
- 更简单的部署过程，不再需要配置nginx等，只要直接git push到github即可。

# 使用github pages

## git的简单使用
在使用github前，首先需要先学会[git](https://git-scm.com/download/win)的简单使用。<font color=Red>将我们的blog内容托管于本地的git仓库之后，才能在后续提交到github上</font>。

根据官网的下载指导，先下载并安装git。

安装完成后，进到站点的C:\Hugo\Sites\example文件夹，执行`git init`，初始化一下git文件夹，这样博客源码文件夹就变成了一个git仓库。

```
PS C:\Hugo\Sites\example> git init
Initialized empty Git repository in C:/Hugo/Sites/example/.git/
```

之后，将使用` git add * `将所有代码暂存，暂存是对当前工作区的改变进行一个非正式的存储，要将代码正式提交到分支上，需再使用` git commit -m 'hugo_blog first commit' `提交代码。

```
PS C:\Hugo\Sites\example> git add *   
PS C:\Hugo\Sites\example> git commit -m 'hugo_blog first commit' 
[main (root-commit) 1a5c9e4] hugo_blog first commit
 130 files changed, 3554 insertions(+)
```

这样，所有的代码都已经提交到git仓库的主分支上并有了一个提交记录了。

## 创建github page仓库
首先，需要注册一个[github](https://github.com/)账号，并登录。

之后，创建一个仓库,并用"用户命.github.io"的形式给这个仓库命名，其他的选项不需要修改，直接点击创建仓库按钮即可。
![创建仓库](/images/new_repo.png)
![命名仓库](/images/named_repo.png)

创建仓库之后，还需要将我们的本地仓库推到新创建的github远程仓库中。

再回到我们的本地博客example的文件夹中，打开命令行，输入以下命令。
```
PS C:\Hugo\Sites\example> git branch -M main
PS C:\Hugo\Sites\example> git remote add origin git@github.com:caspian718/caspian718.github.io.git
PS C:\Hugo\Sites\example> git push -u origin main
Enumerating objects: 174, done.
Counting objects: 100% (174/174), done.
Delta compression using up to 8 threads
Compressing objects: 100% (150/150), done.
Writing objects: 100% (174/174), 2.60 MiB | 1.18 MiB/s, done.
Total 174 (delta 12), reused 0 (delta 0), pack-reused 0      
remote: Resolving deltas: 100% (12/12), done.
To github.com:caspian718/caspian718.github.io.git
 * [new branch]      main -> main
Branch 'main' set up to track remote branch 'main' from 'origin'
```
运行成功之后，在github的仓库中就可以看到上传的内容了。
![内容](/images/contents_in_github.png)

之后我们做一些设置，就可以将该仓库的内容作为github pages发布了。

## 简单设置github actions

依次点击Settings，Code and automation下的Pages，选择一个官方的github actions来为我们build和部署使用hugo框架的博客，github actions是一个CI/CD工具，每次我们向该仓库上传代码时候，github就会帮助我们按照actions的脚本来对代码进行编译和部署。

![选择source](/images/select_source.png)

这里选择GitHub Actions之后，就会弹出github为我们建议的workflow，显然github在对我们仓库中的代码检查之后，意识到我们的博客使用了hugo，所以为我们直接列出了Hugo，这里点击Hugo的Configure即可。

![hugo actions](/images/list_hugo.png)

之后点击Start Commit，github就会帮我们把这个官方CD脚本放到我们的仓库中。

![commit CD](/images/start_commit.png)

之后，在浏览器打开用户命.github.io界面，就会看到我们的博客主页了，至此，我们使用github pages发布博客内容大功告成。

![blog page](/images/blog.png)

## 后续使用
在后续我们需要更新我们的博客时，先在本地添加或修改我们的文章，然后先提交到本地，再push到github上，github actions就会为我们自动编译新的hugo博客，并部署到我们的github pages上。