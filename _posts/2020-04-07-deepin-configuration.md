---
layout: mypost
title: Deepin 安装后的操作笔记
categories: [deepin]
---

## 基础操作（安装之后需要做的工作）

### 当前操作系统版本

```shell
# 查看内核版本
$ cat /proc/version
Linux version 4.15.0-30deepin-generic (pbuilder@zs-PC) (gcc version 6.3.0 20170516 (Debian 6.3.0-18+deb9u1))

# 查看 debian 版本
$ cat /etc/debian_version
9.0

# 查看发行版
$ lsb_release -a
No LSB modules are available.
Distributor ID: Deepin
Description:    Deepin 15.11
Release:        15.11
Codename:       stable
```

### 替换镜像源

参考：[https://developer.aliyun.com/mirror/deepin](https://developer.aliyun.com/mirror/deepin)

备注：原来没有替换这个源时，似乎无法使用 `apt update` 等指令

### 安装 ssh

略

### 远程桌面

1. 安装 xrdp

2. 注销当前登录用户（否则似乎无法登录）
