---
layout: mypost
title: git server 基础操作
categories: [tools,git]
---

### 服务器的搭建

工作系统：Ubuntu 18.04 LTS

1. 添加用户

    ```shell
    # 添加用户，用户名：git
    $ sudo useradd -d /home/git -m -s /bin/bash git
    # 修改密码
    $ sudo passwd git
    Enter new UNIX password:
    Retype new UNIX password:
    passwd: password updated successfully
    # 添加sudo权限
    $ sudo vi /etc/sudoers
    ...
    # User privilege specification
    root    ALL=(ALL:ALL) ALL
    git     ALL=(ALL:ALL) ALL
    ...
    ```

2. 安装 git 全套

    ```shell
    # 几百兆也不，后续还有用到其他功能，索性直接安装全套
    $ sudo apt install git-all
    ...
    ```

3. 配置服务器

    > 参考链接：[https://git-scm.com/book/en/v2/Git-on-the-Server-Setting-Up-the-Server](https://git-scm.com/book/en/v2/Git-on-the-Server-Setting-Up-the-Server)

    Git Server 操作：

    ```shell
    # 切换用户
    $ su git

    # 来到 git 用户家路面
    $ cd ~

    # 创建根仓库repo
    $ mkdir repo

    # 添加测试仓库，仓库名：test.git
    $ git init --bare repo/test.git

    # 新建认证文件，文件路径：~/.ssh/authorized_key
    $ mkdir .ssh
    $ touch .ssh/authorized_keys

    # 添加需要认证用户的公钥（id_rsa.pub）
    $ vi .ssh/authorized_keys
    ```

    Git Client 操作：

    ```shell
    # 测试是否成功 git clone user_name@server_ip:path
    # 似乎更多人喜欢使用绝对路径，这里是相对home的相对路径
    $ git clone git@server_ip:repo/test.git
    Cloning into 'test'...
    warning: You appear to have cloned an empty repository.

    # 尝试提交&推送
    $ cd test/
    $ touch aaa
    $ git add aaa
    $ git commit -m "add aaa"
    $ git push
    ```

4. 禁止开发者使用shell登录：

   1. 添加用户认证 `authorized_key` 之后，开发者可以使用命令 `ssh git@server_addr` 以系统 `git` 用户登录远程服务器。

        ```shell
        $ ssh git@your_server
        Welcome to ......
        ```

   2. 查看 `git-shell` 的路径，将其添加到 `/etc/shells` 中，使用 `chsh` 更改shell的设定。

        ```shell
        $ which git-shell               # 查找 git-shell 路径
        /usr/bin/git-shell
        $ vi /etc/shells                # 把 git-shell 加入有效 shell 列表
        ...
        /usr/bin/git-shell
        ...
        $ sudo chsh git                 # 改变用户 git 登录使用 shell 的路径
        [sudo] password for git:
        Changing the login shell for git
        Enter the new value, or press ENTER for the default
            Login Shell [/bin/bash]: /usr/bin/git-shell
        ```

   3. 再次尝试，提示登录请求被拒绝

        ```shell
        $ ssh git@your_server
        ...
        fatal: Interactive git shell is not enabled.
        hint: ~/git-shell-commands should exist and have read and execute access.
        ...
        ```

5. 搭建 gitweb 服务

    > 参考[https://git-scm.com/book/en/v2/Git-on-the-Server-GitWeb](https://git-scm.com/book/en/v2/Git-on-the-Server-GitWeb)
    >
    > 参考[https://help.ubuntu.com/lts/serverguide/httpd.html](https://help.ubuntu.com/lts/serverguide/httpd.html)
    >
    > 参考[http://httpd.apache.org/docs/2.4/](http://httpd.apache.org/docs/2.4/)
    >
    > 在 [git-scm.com](https://git-scm.com/) 和 [ubuntu.com](https://ubuntu.com/) 可以搜索到比较官方的资料，再配合搜索引擎可以快速找到答案

    ```shell
    # 安装 apache2
    $ sudo apt install apache2
    # 安装完成之后通过浏览器输入服务器地址应该可以看到 apache2 的说明页面

    # 配置仓库路径，配置文件路径：/etc/gitweb.conf
    $ sudo vi /etc/gitweb.conf
    # 修改变量：$projectroot=your_repository_path
    # path to git projects (<project>.git)
    $projectroot = "/home/git/repo";

    # 使能 cgi 模块
    $ sudo a2enmod cgi

    # 重启 apache2 服务
    $ sudo service apache2 restart
    # 此时完成配置，通过浏览器输入：server_ip/gitweb
    ```
