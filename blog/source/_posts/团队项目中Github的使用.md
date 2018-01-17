---
title: 团队项目中Github的使用
date: 2017-08-19 00:39:07
categories: 技术相关
tags: [github, git]
---
** 前几个月学习了Github的使用，希望我的经验能帮助到那些想要了解和学习Github的人** <Excerpt in index | 首页摘要>
    本文将讲解团队项目中Github的基本使用，笔者的操作系统为Win10
<!-- more -->
<The rest of contents | 余下全文>

# 前言

[Github](https://github.com/)是一个面向开源及私有软件项目的托管平台，因为只支持git作为唯一的版本库格式进行托管，故名GitHub。
gitHub于2008年4月10日正式上线，除了git代码仓库托管及基本的 Web管理界面以外，还提供了订阅、讨论组、文本渲染、在线文件编辑器、协作图谱（报表）、代码片段分享（Gist）等功能。目前，其注册用户已经超过350万，托管版本数量也是非常之多，其中不乏知名开源项目 Ruby on Rails、jQuery、python 等。[百度百科](https://baike.baidu.com/item/github/10145341)

# 创建仓库并提交、推送文件到远程仓库

- 在本地操作系统上安装[git](https://git-scm.com/)，这是[下载页面](https://git-scm.com/downloads)，对于git的安装和配置，在此不做介绍。

- 登录[Github](https://github.com/)并点击"New repository"按钮，新建远程仓库。

![新建远程仓库](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/github_usage/new.png)

- 进入新建仓库页面，填写仓库信息，点击"Create repository"按钮，完成远程仓库的创建。

![新建远程仓库](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/github_usage/create.png)

- 出现以下界面说明创建成功。

![新建远程仓库](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/github_usage/created.png)<br>
`注意：`如果在上一个页面没有选择创建.gitignore、LICENSE、README.md等文件，可以之后添加。当前只有master分支（git的默认分支为master），您也可以点击上图中的"Branch:master"下拉按钮，新建分支。

- 在本地新建文件夹，用于存放仓库，文件夹必须为空。在文件夹中按住Shift点击鼠标右键，点击"在此处打开命令窗口"，或直接点击鼠标右键点击"Git Bash Here"（如果没有该选项，则需找到git bash所在位置，启动bash，并导航进入本文件夹）。

- 键入下面的命令初始化本地仓库。

```Bash
git init
```

- 为本地仓库添加远程仓库。

```Bash
git remote add origin https://github.com/CS-Tao/example.git
```

`注意：`该命令的格式为 "git remote add 远程仓库的别名（方便记忆和键入） 远程仓库的url"。

- 拉取远程仓库并合并到本地仓库。

```Bash
git pull origin master
```

`注意：`该命令的格式为 "git pull 远程仓库的别名（或url） 希望拉取的分支"。该命令会自动在本地仓库中创建master分支，另外，"git pull"命令相当于"git fetch"命令和"git merge"命令的集成，在此不再详述。

- 这三个命令的效果如下。

![建立本地仓库](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/github_usage/first_pull.png)

- 假如您现在在本项目文件夹中新建了"NewFolder"文件夹，并在文件夹中添加了source.cpp文件，您可以依次执行以下命令将新建的文件提交到远程仓库。

```Bash
git add NewFolder/source.cpp
git commit -m "Add source.cpp"
git push origin master
```

`注意：`
    1. 无论是新建文件，还是对文件做了修改，都可以键入类似的命令提交并推送文件。
    2. 向库中添加文件的命令格式为，"git add 文件或文件夹"，不同文件或文件夹用空格隔开，添加文件夹时会把文件夹内部的所有文件一并添加。
    3. 在执行"git commit -m "这次提交做了什么""时，git会自动检测文件是否为新建文件或是否做了修改，并将新建或修改的文件或文件夹提交到本地仓库。
    4. "git push 远程仓库的别名（或url） 希望推送到的分支"命令会将本地的提交推送到远程仓库。<br>
![推送到远程](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/github_usage/first_push.png)

- 到此为止，本地仓库和远程仓库的视图如下。

![本地仓库](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/github_usage/folder.png)<br>
![远程仓库](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/github_usage/github_addFile.png)

# 新建本地分支并推送到远程

敬请期待

# fork团队组长的仓库并合并不同成员的提交

敬请期待

# 合并冲突的方法

敬请期待