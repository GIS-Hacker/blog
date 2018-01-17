---
title: 个人独立主页与Hexo博客的集成
date: 2018-01-16 18:38:50
categories: 技术相关
tags: [github, Hexo]
---
最近在写网页的时候，发现了一个很不错的基于Canvas的樱花飘落背景，于是想着怎么把它做成主页嵌入到自己的博客中，最开始笔者是用Github小号建立的Github Pages对该主页进行托管，然后把网页中的所有链接都指向我大号的Github Pages的相关链接。
之后为了代码的通用性，我将该主页改造成了ejs模板，并利用[ejs-on-command](https://github.com/shennan/ejs-on-command)工具将其进行渲染为html，也是为了代码的通用性，我开始研究如何把这两个Github Pages融合为一个，也就是本篇文章将要讲解的内容：如何把个人独立的主页集成到Hexo博客中并利用命令`Hexo d`推送到Github Pages。以[我的主页](http://home.cs-tao.cc/)为例，[主页源码链接](https://github.com/CS-Tao/homepage)。
<!-- more -->

### Hexo博客配置

- 为了实现个人主页和博客的兼容，需要修改Hexo主配置文件中的`root、url、public_dir`的值，比如我的（这样配置后便可以将原博客的源码移动到`public/blog/`文件夹下，博客访问链接变为了`<原网站链接>/blog/`）：
    ```yml
    url: https://cs-tao.github.io/blog/
    root: /blog/
    public_dir: public/blog/
    ```
- 修改hexo-deployer-git工具
    我对hexo-deployer-git工具做了一些修改，在兼容原hexo-deployer-git工具的基础上，添加了`deploy.punlic_dir`配置，若未指定该配置，就直接使用`public_dir`配置的值。如果不对该工具做修改，在执行hexo g命令的时候hexo会将博客发布到`public_dir`配置对应的文件夹下，而在执行命令`hexo d`的时候，我们需要将整个public文件夹发布到远程，这时候需要指定`deploy.punlic_dir`的值，比如我的deploy配置：
    ```yml
    deploy:
    - type: git
      repository: https://github.com/CS-Tao/CS-Tao.github.io.git
      branch: master
      public_dir: public
    ```
    修改之后的hexo-deployer-git请到[https://github.com/CS-Tao/hexo-deployer-git](https://github.com/CS-Tao/hexo-deployer-git)下载，下载后替换掉Hexo博客根目录下的`node_modules\hexo-deployer-git`即可。
- 删除原`public`文件夹中的内容，将主页源码放入`public`文件夹
- 生成博客并推送到Github
    ```bash
    hexo g
    hexo d
    ```
---
[博客源码链接](https://github.com/CS-Tao/CS-Tao.github.io)
