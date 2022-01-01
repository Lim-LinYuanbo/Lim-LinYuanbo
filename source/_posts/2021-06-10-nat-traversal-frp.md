---
#layout: mypost
title: 内网穿透-FRP
categories:
- [运维]
tags:
- 运维
---

> 因为在家办公需要连接公司服务器，所以不得不使用内网穿透，记录一下配置笔记。

### 网络拓扑图

```c
+--------------+       +-------------+      +----------------------------+       +-----------------+          +-----------------+
|              |       |             |      |                            |       |                 |          |                 |
|  personal PC +------>+   gateway   +----->+    public network server   +------>+     gateway     +--------->+  private server |
|              |       |             |      |                            |       |                 |          |                 |
+--------------+       +-------------+      +----------------------------+       +-----------------+          +-----------------+

说明：个人电脑通过公网服务器，连接公司内网服务器。
```

### FRP 介绍

frp 是一个专注于内网穿透的高性能的反向代理应用，支持 TCP、UDP、HTTP、HTTPS 等多种协议。可以将内网服务以安全、便捷的方式通过具有公网 IP 节点的中转暴露到公网。

仓库地址：[https://github.com/fatedier/frp](https://github.com/fatedier/frp)

参考章节：[Access your computer in LAN by SSH](https://github.com/fatedier/frp#access-your-computer-in-lan-by-ssh)

开机启动服务学习参考：[Systemd 入门教程：命令篇](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-commands.html) [Systemd 入门教程：实战篇](http://www.ruanyifeng.com/blog/2016/03/systemd-tutorial-part-two.html)

> 阮一峰 上面的这两篇文章写得真棒，奥力给！！！:yum:

### 服务器配置(服务端后缀s，例如：frps)

1. 基础配置

    ```shell
    $ lsb_release -a
    LSB Version:    :core-4.1-aarch64:core-4.1-noarch
    Distributor ID: CentOS
    Description:    CentOS Linux release 7.9.2009 (AltArch)
    Release:        7.9.2009
    Codename:       AltArch
    # 服务器是 ARM 架构的服务器
    $ uname -i
    aarch64

    # 下载 ARM 架构的包
    $ wget https://github.com/fatedier/frp/releases/download/v0.37.0/frp_0.37.  0_linux_arm64.tar.gz

    $ tar -zxvf frp_0.37.0_linux_arm64.tar.gz
    # 名字贼长......重命名一下...
    $ mv frp_0.37.0_linux_arm64 frp

    # 查看当前开放的端口,确认没有使用监听的端口,例如:40000 没有被使用
    $ netstat -antulp
    # 修改配置文件,记得打开防火墙与指定的端口,例如:使用 40000
    $ vi frp/frps.ini
    [common]
    bind_port = 40000
    # 启动服务
    $ ./frp/frps -c ./frp/frps.ini
    2021/06/10 20:27:45 [I] [root.go:200] frps uses config file: ./frp/frps.ini
    2021/06/10 20:27:45 [I] [service.go:192] frps tcp listen on 0.0.0.0:40000
    2021/06/10 20:27:45 [I] [root.go:209] frps started successfully
    ...
    # 打开另外一个终端,查看端口情况,确认上面使用的端口确实已经处于监听状态
    $ netstat -antulp
    Proto Recv-Q Send-Q Local Address           Foreign Address             State       PID/Program name
    tcp6       0      0 :::40000                :::*                        LISTEN      2072/./frp/frps
    ```

2. 添加服务

    ```shell
    # 上面验证成功之后,添加服务
    $ sudo cp frp/systemd/frps.service /usr/lib/systemd/system/
    # 修改服务配置,如下:
    [Unit]
    Description=Frp Server Service
    After=network.target

    [Service]
    Type=simple
    User=root # 不知为何,这里需要改为 root 才能正常启动
    Restart=on-failure
    RestartSec=5s
    ExecStart=/home/linyuanbo/frp/frps -c /home/linyuanbo/frp/frps.ini # 执行指令,  你的 frp 程序+配置文件

    [Install]
    WantedBy=multi-user.target

    # 从新加载服务,启动,查看状态,开机启动服务
    $ sudo systemctl daemon-reload
    $ sudo systemctl start frps.service
    ● frps.service - Frp Server Service
       Loaded: loaded (/usr/lib/systemd/system/frps.service; disabled; vendor   preset: disabled)
       Active: active (running) since Thu 2021-06-10 20:39:37 CST; 17s ago
     Main PID: 2440 (frps)
       CGroup: /system.slice/frps.service
               └─2440 /home/linyuanbo/frp/frps -c /home/linyuanbo/frp/frps.ini
    ...
    $ sudo systemctl status frps.service
    sudo systemctl enable frps.service
    Created symlink from /etc/systemd/system/multi-user.target.wants/frps.service   to /usr/lib/systemd/system/frps.service.

    # 重启确认,完毕
    ```

### 客户端配置(客户端后缀c，例如：frpc)

1. 客户端操作记录

    ```shell
    # 下载,解压,重命名,修改配置文件
    vi frp/frpc.ini
    [common]
    server_addr = xxx.xxx.xxx.xxx # 服务器地址
    server_port = 40000 # 上面配置的服务器监听端口

    [ssh]
    type = tcp
    local_ip = 127.0.0.1 # 本地服务地址
    local_port = 22 # 本地服务端口,例如:我需要使用 SSH 服务
    remote_port = 40001 # 远程端口使用 40001 吧,任意,确保服务器端口开放

    # 启动服务
    $ ./frp/frpc -c ./frp/frpc.ini
    2021/06/10 12:51:15 [I] [service.go:304] [...] login to server success, get     run id [...], server udp port [0]
    2021/06/10 12:51:15 [I] [proxy_manager.go:144] [...] proxy added: [ssh]
    2021/06/10 12:51:15 [I] [control.go:180] [...] [ssh] start proxy success
    ```

2. 服务端操作记录

    ```shell
    # 确认刚刚设置的远程端口 40001 确实已经处于监听状态
    $ netstat -antulp
    tcp6       0      0 :::40000                :::*                        LISTEN      -
    tcp6       0      0 :::40001                :::*                        LISTEN      -
    ```

### 验证成功

使用 putty 简单粗暴的验证，输入服务器 IP 地址,远程端口(上面设置的 40001 )。

验证成功，可以访问内网的服务器，搞定收工！
