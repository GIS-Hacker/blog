---
title: Ubuntu系统下的PostgreSQL安装和配置
date: 2017-09-02 23:20:58
categories: 技术相关
tags: [PostgreSQL, Ubuntu]
---
** Ubuntu 16.04系统下的PostgreSQL 9.6安装和配置的详细步骤** <Excerpt in index | 首页摘要>
PostgreSQL 是一个自由的对象-关系数据库服务器(数据库管理系统)，它在 BSD-风格许可证下发行
<!-- more -->
<The rest of contents | 余下全文>

# PostgreSQL介绍

[PostgreSQL](https://www.postgresql.org/)是以加州大学伯克利分校计算机系开发的 POSTGRES，现在已经更名为PostgreSQL，版本 4.2为基础的对象关系型数据库管理系统（ORDBMS），开发语言为C/C++。PostgreSQL支持大部分 SQL标准并且提供了许多其他现代特性：复杂查询、外键、触发器、视图、事务完整性、MVCC。同样，PostgreSQL 可以用许多方法扩展，比如， 通过增加新的数据类型、函数、操作符、聚集函数、索引。免费使用、修改、和分发 PostgreSQL，不管是私用、商用、还是学术研究使用。

# PostgresSQL安装

- 添加apt-repository
    ```Bash
    sudo add-apt-repository "deb http://apt.postgresql.org/pub/repos/apt/ xenial-pgdg main"
    ```
- 载入apt-repository的签名
    ```Bash
    wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
    ```
- 更新package列表
    ```Bash
    sudo apt-get update
    ```
- 通过apt-get工具安装PostgreSQL
    ```Bash
    apt-get install postgresql-9.6
    ```

# 配置PostgreSQL

- 切换到postgres用户
    ```Bash
    sudo su postgres
    ```
- 登录到postgresql
    ```Bash
    psql postgres
    ```
    如果看到如下页面则说明之前的努力没有白费，已经安装成功了。<br>
    ![Putty截图](https://raw.githubusercontent.com/CS-Tao/github-content/master/contents/blog/image/PostgreSQL%E5%AE%89%E8%A3%85%E6%88%90%E5%8A%9F.png)
- 更改用户密码
    在当前界面下输入
    ```Bash
    \password
    ```
    输入你想设置的PostgreSQL密码。输入\q回车退出。
- 设置连接权限
    - 打开配置文件
        ```Bash
        vim /etc/postgresql/9.1/main/postgresql.conf
        ```
    - 修改连接权限为所有主机
        ```Bash
        #listen_addresses = ‘localhost’改为 listen_addresses = ‘*’
        ```
        `注意：`需要去掉#号
    - 启用密码验证
        ```Bash
        #password_encryption = on 改为 password_encryption = on
        ```
        `注意：`需要去掉#号
- 设置用户ip段
    - 打开配置文件
        ```Bash
        vim /etc/postgresql/9.1/main/pg_hba.conf
        ```
    - 在文件末尾添加如下内容
        ```Bash
        host all all 0.0.0.0/0 md5
        ```
        `注意：`0.0.0.0为地址段。0为掩码的二进制位，可取数值为0、8、16、24、32。md5为加密方式
        `示例：`192.168.0.0/16代表192.168.0.1 ~ 192.168.255.254
- 重启PostgreSQL服务
    ```Bash
    sudo service postgres restart
    ```

# 登录数据库

- 本地登录
    ```Bash
    psql -U postgres -h 127.0.0.1
    ```
- 远程登录
    ```Bash
    psql -U postgres -h 远程IP地址
    ```