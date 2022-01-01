---
#layout: mypost
title: git 常用基础操作
categories:
- [git]
tags:
- git
---

### 常用基础操作

1. 生成ssh key：`ssh-keygen -t rsa -C "your comment"`

    e.g.：`ssh-keygen -t rsa -C "your_email@example.com"`

2. 公钥路径：`~/.ssh/id_rsa.pub`

3. 指定忽略未追踪的文件 gitignore

    > 参考：[https://git-scm.com/docs/gitignore](https://git-scm.com/docs/gitignore)
    >
    > 参考：[https://github.com/github/gitignore](https://github.com/github/gitignore)

    ```shell
    # 文件夹目录结构（忽略 .git 文件夹）
    $ tree -I .git
    .
    ├── inc             # ✓
    │   ├── aaa.h
    │   ├── bbb.h
    │   └── ccc.h
    ├── main            # ✓
    ├── main.c          # ✓
    ├── src
    │   ├── aaa.c       # ✓
    │   ├── aaa.o       # ✗
    │   ├── bbb.c       # ✓
    │   ├── bbb.o       # ✗
    │   ├── ccc.c       # ✓
    │   └── ccc.o       # ✗
    └── tmp             # ✗
        ├── a.txt
        ├── b.txt
        └── c.txt

    # 查看 git 忽略追钟配置文件
    $ cat .gitignore
    .gitignore          # 忽略该配置文件
    /tmp                # 忽略文件夹
    *.o                 # 忽略所有 .o 文件

    # 查看仓库根目录状态
    $ git status
    On branch master
    No commits yet
    Untracked files:
    (use "git add <file>..." to include in what will be committed)
            inc/
            main
            main.c
            src/
    nothing added to commit but untracked files present (use "git add" to track)

    # 添加本地所有追踪文件
    $ git add .
    # 查看仓库根目录状态
    $ git status
    On branch master
    No commits yet
    Changes to be committed:
    (use "git rm --cached <file>..." to unstage)
            new file:   inc/aaa.h
            new file:   inc/bbb.h
            new file:   inc/ccc.h
            new file:   main
            new file:   main.c
            new file:   src/aaa.c
            new file:   src/bbb.c
            new file:   src/ccc.c
    ```

4. 代理

    参考：[https://git-scm.com/docs/git-config](https://git-scm.com/docs/git-config)

    > `http.proxy`
    >
    > `Override the HTTP proxy, normally configured using the http_proxy, https_proxy, and all_proxy environment variables (see curl(1)). In addition to the syntax understood by curl, it is possible to specify a proxy string with a user name but no password, in which case git will attempt to acquire one in the same way it does for other credentials. See gitcredentials[7] for more information. The syntax thus is [protocol://][user[:password]@]proxyhost[:port]. This can be overridden on a per-remote basis; see remote.<name>.proxy`

    设置格式：`git config --global [protocol://][user[:password]@]proxyhost[:port]`

    取消格式：`git config --global --unset [value-regex]`

    查看格式：`git config --global --get [value-regex]`

    实例：

    ```shell
    # 来到家目录，方便查看 .gitconfig
    $ cd ~

    # 设置 http、https 代理（我是在 WSL 下跑的，SSRR 的代理端口我改成了 60000）
    $ git config --global http.proxy socks5://127.0.0.1:60000
    $ git config --global https.proxy socks5://127.0.0.1:60000
    $ cat .gitconfig
    [http]
        proxy = socks5://127.0.0.1:60000
    [https]
        proxy = socks5://127.0.0.1:60000

    # 查看 http、https 代理配置情况
    $ git config --global --get http.proxy
    socks5://127.0.0.1:60000
    $ git config --global --get https.proxy
    socks5://127.0.0.1:60000

    # 取消 http、https 代理配置
    $ git config --global --unset http.proxy
    $ git config --global --unset https.proxy
    ```
