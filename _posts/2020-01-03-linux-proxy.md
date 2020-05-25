---
layout: mypost
title: Linux Proxy
categories: [科学上网,Linux]
---

## [proxychains](https://github.com/haad/proxychains)

### 步骤(参考[proxychains readme](https://github.com/haad/proxychains/blob/master/README.adoc))

1. 安装

    ```shell
    sudo apt-get install proxychains
    ```

2. 配置

    ```shell
    # 建立一个proxychains文件夹(可选)
    mkdir .proxychains
    # 懒得重新编辑,所以直接拷贝一个过来
    cp /etc/proxychains.conf ~/.proxychains/
    # 修改配置
    vi ~/.proxychains/proxychains.conf
    ```

    ```txt
    # proxychains.conf 修改部分
    [ProxyList]
    #add proxy here ...
    # meanwile
    # defaults set to "tor"
    socks5  127.0.0.1 60000 # 60000是我代理的端口
    ```

3. 实例&&测试

    ```shell
    # 没有代理情况下访问Google
    $ curl www.google.com
    curl: (7) Failed to connect to www.google.com port 80: Connection refused

    # 启用代理之后访问Google
    $ proxychains curl www.google.com
    ... # 应该是会有一堆打印
    ```

4. 其他

    ```shell
    # 每次输入proxychains有点麻烦
    # 查看使用的 sh 类型
    $ ps -e
    PID TTY         TIME CMD
    1   ?           00:00:00 init
    7   tty1        00:00:00 init
    8   tty1        00:00:00 bash
    21  tty1        00:00:00 ps
    # 让 bash 所有网络请求都通过代理
    $ proxychains bash
    # 测试
    $ curl www.google.com
    ... # 同步骤3中的打印
    ```

## [polipo](https://github.com/jech/polipo)

1. 安装

    ```shell
    sudo apt-get install polipo
    ```

2. 说明

    ```shell
    $ man polipo # 可以看见一些选项和文件
    ...
    $ polipo -h # 同上,能看到的东西不多
    ...
    $ sudo polipo -v # 看到这么一大段 :)
    configFile /etc/polipo/config Configuration file.
    ...
    $ cat /etc/polipo/config
    # This file only needs to list configuration variables that deviate
    # from the default values.  See /usr/share/doc/polipo/examples/config.sample # 这里有例子
    # and "polipo -v" for variables you can tweak and further information.

    logSyslog = true
    logFile = /var/log/polipo/polipo.log
    ```

3. 配置
4. 测试
5. 其他
