---
layout: mypost
title: ubuntu SSH Server 配置
categories: [运维,ubuntu]
---

### SSH Server 配置

1. 配置文件路径：

    ```shell
    # 服务器端向客户端请求消息的时间间隔，单位：秒
    ClientAliveInterval 60
    # 服务器发出请求后客户端超时次数
    ClientAliveCountMax 300

    # 这是我的经验值，不知为何配置成其他的似乎不生效?
    ```

2. 重启服务生效：

    ```shell
    $ sudo /etc/init.d/ssh restart
    [ ok ] Restarting ssh (via systemctl): ssh.service.
    ```
