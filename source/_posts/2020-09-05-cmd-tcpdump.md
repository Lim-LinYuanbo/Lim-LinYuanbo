---
#layout: mypost
title: tcpdump 基础使用笔记
categories:
- [运维]
tags:
- 运维
---

## 描述

Tcpdump  prints  out  a  description  of the contents of packets on a network interface that match the boolean expression; the description is preceded by a time stamp, printed, by default, as hours, minutes, seconds,  and fractions of a second since midnight.

## 帮助

```shell
$ tcpdump -h
tcpdump version 4.9.2
libpcap version 1.8.1
OpenSSL 1.1.1  11 Sep 2018
Usage: tcpdump [-aAbdDefhHIJKlLnNOpqStuUvxX#] [ -B size ] [ -c count ]
                [ -C file_size ] [ -E algo:secret ] [ -F file ] [ -G seconds ]
                [ -i interface ] [ -j tstamptype ] [ -M secret ] [ --number ]
                [ -Q in|out|inout ]
                [ -r file ] [ -s snaplen ] [ --time-stamp-precision precision ]
                [ --immediate-mode ] [ -T type ] [ --version ] [ -V file ]
                [ -w file ] [ -W filecount ] [ -y datalinktype ] [ -z postrotate-command ]
                [ -Z user ] [ expression ]

```

## 实操

1. 查看当前系统状态

    ```shell
    # 查看当前版本
    $ lsb_release -a
    No LSB modules are available.
    Distributor ID: Ubuntu
    Description:    Ubuntu 18.04.1 LTS
    Release:        18.04
    Codename:       bionic


    # 查看现在查看当前网络设备状态
    $ ifconfig
    ```

2. 简单使用案例

    ```shell
    # 参数说明:-i 指定网卡名, -s 数据包大小(0:表示抓取整个数据包), -s 指定抓取内容写入文件名
    $ sudo tcpdump -i eth0 -s 0 -w test.cap

    # 参数说明:-X 以16进制呈现 -r 读取数据包名称
    $ sudo tcpdump -X -r test.cap
    # 内容略,看过TCP/IP协议的人应该都可以看懂

    # 参数说明:-n 不把主机的网络地址转换成名字, ip/icmp 指定抓取网络协议类型
    $ sudo tcpdump -i eth0 -n ip
    $ sudo tcpdump -i eth0 -n icmp

    # 参数说明:src/dst host 113.92.130.87:数据包来源/目的 主机名 IP地址
    $ sudo tcpdump -i eth0 -n host 113.92.130.87
    $ sudo tcpdump -i eth0 -n src host 113.92.130.87
    $ sudo tcpdump -i eth0 -n dst host 113.92.130.87

    # 参数说明:-n 不把主机的网络地址转换成名字, tcp 指定抓取协议类型, port 80 指定抓取端口号
    $ sudo tcpdump -i eth0 -n tcp port 80
    # 参数说明:port ! 80 表示抓取非80端口数据包
    $ sudo tcpdump -i eth0 -n tcp port ! 80
    ```

## 其他

1. wireshark 也是一款有名的图形化抓包工具,可以将抓取的数据包内容使用该软件打开

2. 目前发现,在 WSL 下似乎无法使用 tcpdump,提示如下(目前没有解决):`tcpdump: socket: Socket type not supported`
