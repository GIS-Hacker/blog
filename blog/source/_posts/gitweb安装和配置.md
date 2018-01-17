---
title: gitweb安装和配置
date: 2017-10-19 15:35:55
categories: 技术相关
tags: [gitweb, Apache]
---
** 基于Apache服务器的gitweb安装和配置** <Excerpt in index | 首页摘要>
gitweb需要使用的配置文件的位置和功能。操作系统：Ubuntu 16.04 LTS
<!-- more -->
<The rest of contents | 余下全文>

# gitweb安装和配置（在前人的基础上做了一些添加和修改）

[原文地址](http://blog.csdn.net/qq_25667339/article/details/53083968)

## 安装gitweb和Apache

```bash
sudo apt-get install gitweb apache2
```

## 修改/etc/gitweb.conf

```bash
vim /etc/gitweb.conf
```

内容如下：

```bash
$projectroot = "/home/git/repositories";

$git_temp = "/tmp";

$projects_list = $projectroot;

@stylesheets = ("../gitweb/static/gitweb.css");

$javascript = "../gitweb/static/gitweb.js";

$logo = "../gitweb/static/git-logo.png";

$favicon = "../gitweb/static/git-favicon.png";

@diff_opts = ();
```

保存退出

## 修改/etc/apache2/conf-available/gitweb.conf

```bash
vim /etc/apache2/conf-available/gitweb.conf
```

内容如下：

```xml
Alias /gitweb /usr/share/gitweb
<Directory /usr/share/gitweb>
    Options +FollowSymLinks +ExecCGI
    AddHandler cgi-script .cgi
    AuthType Basic
    AuthName "Restricted Content"
    AuthUserFile /home/git/.htpasswd
    Require valid-user
</Directory>
```

保存退出

`注意：`"AuthUserFile"是认证文件位置，用如下命令生成认证文件并添加一个访问用户：

```bash
htpasswd -c 认证文件位置 用户名
```

然后根据提示输入密码即可。

## 使cgi生效

```bash
sudo a2enmod cgi
sudo service apache2 restart
```

## 访问gitweb

如果搭建在本地，访问[http://localhost/gitweb](http://localhost/gitweb)并登录就可看到gitweb设置的git库根目录下的所有项目信息。

但此时访问[http://localhost/cgi-bin/gitweb.cgi](localhost/cgi-bin/gitweb.cgi)不用登录也能访问到项目信息。笔者直接删除了"/usr/lib/cgi-bin/"文件夹中与gitweb相关的文件。其他人便不能通过该路径访问。

## 配置布局

可以使用别人已经写好的布局和图标，详见[github](https://github.com/kogakure/gitweb-theme)

# 注意事项

- 需要将"/usr/share/gitweb"文件夹下的文件和文件夹设置正确的权限，<其他用户>必须有读取文件权限和执行文件权限。缺少读文件的权限服务器会返回"Internal Server Error(500)"错误，缺少执行文件的权限服务器会返回"Forbidden(403)"错误。读取文件权限为4，执行文件权限为1，也就是说<其他用户>的权限至少为'5'。如下，笔者设置的'755'权限的最后一个'5'对应<其他用户>的权限。

    ~~~bash
    cd /usr/share/gitweb/
    sudo chmod -R 755 .
    ~~~

- 旧版本Apache的/etc/apache2/conf.d/gitweb和新版本的/etc/apache2/conf-available/gitweb的是同一个目录。

# 配置文件备忘

## 主配置文件

- 文件位置："/etc/gitweb.conf"
- 文件功能：设置项目集根目录、临时文件目录、布局文件位置及资源文件位置等。

## Apache中gitweb的配置

- 文件位置："/etc/apache2/conf-available/gitweb.conf"
- 文件功能：指定CGI文件位置、认证文件位置等。
- 主要内容：

```xml
<IfDefine ENABLE_GITWEB>
    Alias /gitweb /usr/share/gitweb
    <Directory /usr/share/gitweb>
        Options +FollowSymLinks +ExecCGI
        AddHandler cgi-script .cgi
        AuthType Basic
        AuthName "Restricted Content"
        AuthUserFile /home/git/.htpasswd
        Require valid-user
    </Directory>
</IfDefine>
```