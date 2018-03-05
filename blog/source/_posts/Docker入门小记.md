---
title: Docker入门小记
date: 2018-03-05 15:56:20
categories: 技术相关
tags: [docker]
---
<img src="https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/docker.png" width="45%" height="45%">
原计划是准备寒假在家学习Docker的，但很无奈的是，回到家的我完全不知道如何写下新年的第一段代码，很自然地整个寒假就在吃和玩中度过了，虽然我的寒假前后只有10天，但毕竟是过年啊，玩得特别开心！</br>不过说来也巧，回到学校后，由于某些不可抗力，搁置已久的老挝消防项目又得开始了，原来负责服务器端Docker部署的某大神学长外出实习，重新部署Docker的任务就落在了我的头上（无奈.jpg）。</br>我原本是不准备记录下这篇文章的，但是当我写完了Dockerfile，向服务器部署的时候发现，这次部署可能需要 —— 三个小时？一个人在图书馆坐着，像看剧一样地看着命令终端不断输出完成进度，实在无聊，于是去满楼溜达了半小时，然后看了会书，回到电脑旁，不知道有什么事可以做，就把最近两天学到的知识记录一下吧。
<!-- more -->

### Docker是什么？

Docker是DotCloud公司开发的基于LXC（Linux Container，一种内核虚拟化技术）的高级容器引擎，使用go语言编写（突然发现很多有意思的工具都是用go写的诶），使用Apache 2.0开源协议，源代码托管在Github上。

### 安装Docker

#### 设置存储库

关于不同操作系统Docker的安装方法，[Docker官方文档](https://docs.docker.com/)写得很详细，因为Docker官方推荐使用Ubuntu部署Docker（Ubuntu系统本身就实现了Docker容器运行需要的AUFS机制），所以这里只记录一下Ubuntu 16.04系统下Docker CE的安装。

- 更新apt包索引
    ```bash
    $ sudo apt-get update
    ```
- 安装软件包，用于允许apt通过HTTPS访问Docker安装源
    ```bash
    $ sudo apt-get install \
        apt-transport-https \
        ca-certificates \
        curl \
        software-properties-common
    ```
- 添加Docker的官方GPG密钥：
    ```bash
    $ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```
    确认一下您已经获取到了指纹：
    ```bash
    $ sudo apt-key fingerprint 0EBFCD88

    pub   4096R/0EBFCD88 2017-02-22
          Key fingerprint = 9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
    uid                  Docker Release (CE deb) <docker@docker.com>
    sub   4096R/F273FCD8 2017-02-22
    ```
- 添加储存库：
    ```bash
    $ sudo add-apt-repository \
    "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
    $(lsb_release -cs) \
    stable"
    ```

#### 安装Docker CE

- 更新apt包索引
    ```bash
    $ sudo apt-get update
    ```
- 安装Docker CE
    ```bash
    $ sudo apt-get install docker-ce
    ```

### Docker基础命令记录

因为Docker的运行绑定了`/var/run/docker.sock`文件，用于docker的守护进程（Docker daemon）和容器进程之间通信，而该文件的访问权限为660，必须使用sudo命令才有该文件的执行权限。

#### 容器的生命周期管理

- run
- start/stop/restart
- kill
- rm
- pause/unpause
- create
- exec

#### 容器操作

- ps
- inspect
- top
- attach
- events
- logs
- wait
- export
- port

#### 容器rootfs命令

- commit
- cp
- diff

#### 镜像仓库操作

- login
- pull
- push
- search

#### 本地镜像管理

- images
- rmi
- tag
- build
- history
- save
- import

#### Docker信息

- info
- version

暂存一下，先去吃个饭...