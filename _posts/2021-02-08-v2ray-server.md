---
layout: mypost
title: v2ray Linux 服务端配置
categories: [科学上网]
---

> 由于某种原因，所以科学上网是必不可少的，使用网上的一键搭建的脚本也可以完成，但是想自己试试。

## 1. 安装

参考：[https://www.v2ray.com/en/welcome/install.html](https://www.v2ray.com/en/welcome/install.html)

```shell
// 下载官方安装脚本
$ wget https://raw.githubusercontent.com/v2fly/fhs-install-v2ray/master/install-release.sh

// 添加执行权限
$ chmod +x install-release.sh

// 简单看一下脚本，可以通过 --help 查看帮助
$ ./install-release.sh --help
WARNING: The user currently executing this script is not root. You may encounter the insufficient privilege error.
Are you sure you want to continue? [y/n] y
Continuing the installation with current user...
usage: ./install-release.sh [--remove | --version number | -c | -f | -h | -l | -p]
  [-p address] [--version number | -c | -f]
  --remove        Remove V2Ray
  --version       Install the specified version of V2Ray, e.g., --version v4.18.0
  -c, --check     Check if V2Ray can be updated
  -f, --force     Force installation of the latest version of V2Ray
  -h, --help      Show help
  -l, --local     Install V2Ray from a local file
  -p, --proxy     Download through a proxy server, e.g., -p http://127.0.0.1:8118 or -p socks5://127.0.0.1:1080

// 管理员权限执行安装
$ sudo ./install-release.sh
......
// 第一次安装，所以这些服务文件是没有的，所以无法删除，正常现象
rm: cannot remove ‘/etc/systemd/system/v2ray.service.d/10-donot_touch_multi_conf.conf’: No such file or directory
rm: cannot remove ‘/etc/systemd/system/v2ray@.service.d/10-donot_touch_multi_conf.conf’: No such file or directory
// 提示成功安装
info: Systemd service files have been installed successfully!
......
// 提示版本比较陈旧，不管
warning: The systemd version on the current operating system is too low.
warning: Please consider to upgrade the systemd or the operating system.
// 部署的文件位置
installed: /usr/local/bin/v2ray
installed: /usr/local/bin/v2ctl
installed: /usr/local/share/v2ray/geoip.dat
installed: /usr/local/share/v2ray/geosite.dat
installed: /usr/local/etc/v2ray/config.json
installed: /var/log/v2ray/
installed: /var/log/v2ray/access.log
installed: /var/log/v2ray/error.log
installed: /etc/systemd/system/v2ray.service
installed: /etc/systemd/system/v2ray@.service
......
// 不清楚为啥需要删除 curl 和 unzip，先不管
You may need to execute a command to remove dependent software: yum remove curl unzip
// 通过执行如下命令启动 v2ray
Please execute the command: systemctl enable v2ray; systemctl start v2ray

// 开机启动 v2ray 和 启动 v2ray
$ sudo systemctl enable v2ray
Created symlink from /etc/systemd/system/multi-user.target.wants/v2ray.service to /etc/systemd/system/v2ray.service.
$ sudo systemctl start v2ray
// 查看 v2ray 服务状态
$ systemctl status v2ray
● v2ray.service - V2Ray Service
   Loaded: loaded (/etc/systemd/system/v2ray.service; enabled; vendor preset: disabled)
  Drop-In: /etc/systemd/system/v2ray.service.d
           └─10-donot_touch_single_conf.conf
   Active: active (running) since Wed 2021-04-21 20:29:00 CST; 44s ago
     Docs: https://www.v2fly.org/
 Main PID: 27156 (v2ray)
   CGroup: /system.slice/v2ray.service
           └─27156 /usr/local/bin/v2ray -config /usr/local/etc/v2ray/config.json
```

通过安装信息，有如下路径：

v2ray 命令路径：`/usr/local/bin/v2ray`
配置文件路径：`/usr/local/etc/v2ray/config.json`

## 2.配置

```shell
# 查看当前端口状态
$ sudo netstat -antlp
# 没有找到 v2ray 相关端口

# 修改配置文件 进站端口:10086
$ sudo vi /usr/local/etc/v2ray/config.json
{
  "inbounds": [{
    "port": 10086, // Port of the server. Must be the same as above.
    "protocol": "vmess",
    "settings": {
      "clients": [{ "id": "b831381d-6324-4d53-ad4f-8cda48b30811" }]
    }
  }],
  "outbounds": [{
    "protocol": "freedom",
    "settings": {}
  }]
}

# 重启服务
$ sudo systemctl restart v2ray

# 查看当前端口状态
$ sudo netstat -antlp
tcp6       0      0 :::10086                :::*                    LISTEN      28487/v2ray

# 查看服务器IP
$ curl cip.cc
IP      : BBB.BBB.BBB.BBB
```

## 3.测试

```shell
# 客户端没有代理前的IP
$ curl cip.cc
IP      : AAA.AAA.AAA.AAA

# 修改配置文件
$ sudo vi /usr/local/etc/v2ray/config.json
# 不贴了，参考:https://www.v2ray.com/en/welcome/start.html

# 重启服务,通过代理确认IP,成功代理
$ sudo systemctl restart v2ray.service
$ proxychains curl cip.cc
IP      : BBB.BBB.BBB.BBB
```
