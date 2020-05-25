---
layout: mypost
title: Nginx 虚拟主机搭建记录
categories: [运维,nginx]
---

> Nginx版本：nginx/1.14.0 (Ubuntu)
>
> 服务端环境：Ubuntu 18.04.1 LTS
>
> 客户端环境：Windows 10

### 目标

1. 搭建两个虚拟主机，域名分别为：hello.com，world.com

2. 两台虚拟主机网页内容分别显示:hello，world

### 实操记录

1. 虚拟主机 hello：

    ```shell
    # 创建配置文件
    $ sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/hello.conf

    # 修改内容，参考 default 配置中的虚拟主机介绍
    $ sudo vi /etc/nginx/sites-available/hello.conf
    server {
            listen 80;
            listen [::]:80;

            # 域名
            server_name hello.com;
            #目录
            root /var/www/html/hello.com;
            index index.html;

            location / {
                    try_files $uri $uri/ =404;
            }
    }

    # 创建链接
    $ sudo ln -s /etc/nginx/sites-available/hello.conf /etc/nginx/sites-enabled/

    # 创建目录
    $ sudo mkdir /var/www/html/hello.com

    # 创建主页&&添加主页内容
    $ sudo vi /var/www/html/hello.com/index.html
    <html><body>hello</body></html>
    ```

2. 虚拟主机 world：

    ```shell
    # 创建配置文件
    $ sudo cp /etc/nginx/sites-available/default /etc/nginx/sites-available/world.conf

    # 修改内容，参考 default 配置中的虚拟主机介绍
    $ sudo vi /etc/nginx/sites-available/world.conf
    server {
            listen 80;
            listen [::]:80;

            # 域名
            server_name world.com;
            #目录
            root /var/www/html/world.com;
            index index.html;

            location / {
                    try_files $uri $uri/ =404;
            }
    }

    # 创建链接
    $ sudo ln -s /etc/nginx/sites-available/world.conf /etc/nginx/sites-enabled/

    # 创建目录
    $ sudo mkdir /var/www/html/world.com

    # 创建主页&&添加主页内容
    $ sudo vi /var/www/html/world.com/index.html
    <html><body>world</body></html>
    ```

3. 配置完成，重新读取配置：`sudo systemctl reload nginx.service`

### 测试

1. 修改客户端hosts

    ```shell
    # 服务器IP      域名
    192.168.0.103   hello.com
    192.168.0.103   world.com
    ```

2. 浏览器访问测试
