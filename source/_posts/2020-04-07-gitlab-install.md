---
#layout: mypost
title: gitlab 安装笔记
categories:
- [运维]
tags:
- 运维
- gitlab
---

> 系统版本：ubuntu 18.04 LTS

### 安装记录

参考:[https://about.gitlab.com/install/#ubuntu](https://about.gitlab.com/install/#ubuntu)

1. 查看当前系统版本

    ```shell
    $ cat /proc/version
    Linux version 5.3.0-46-generic (buildd@lcy01-amd64-013) (gcc version 7.5.0 (Ubuntu 7.5.0-3ubuntu1~18.04)) #38~18.04.1-Ubuntu SMP Tue Mar 31 04:17:56 UTC 2020
    ```

    内核版本：5.3.0

    发行版：ubuntu 18.04

2. 选择 Gitlab 适配版本

    选择 清华大学开源软件镜像源 路径：`/gitlab-ce/ubuntu/pool/bionic/main/g/gitlab-ce`

3. 安装

    ```shell
    # 安装必要依赖
    $ sudo apt-get install -y curl openssh-server openssh-client
    $ sudo apt-get install -y ca-certificates
    $ sudo apt-get install -y postfix
    # 选择 Internet Site 填写邮箱
    # 填写邮箱（目前我是瞎填的）

    # 开机启动与使能 SSH 服务
    $ sudo systemctl enable ssh && sudo systemctl start ssh
    Synchronizing state of ssh.service with SysV service script with /lib/systemd/systemd-sysv-install.
    Executing: /lib/systemd/systemd-sysv-install enable ssh

    # 开机启动与使能 POSTFIX 服务
    $ sudo systemctl enable postfix && sudo systemctl start postfix
    Synchronizing state of postfix.service with SysV service script with /lib/systemd/systemd-sysv-install.
    Executing: /lib/systemd/systemd-sysv-install enable postfix

    # 下载
    $ wget https://mirrors.tuna.tsinghua.edu.cn/gitlab-ce/ubuntu/pool/bionic/main/g/gitlab-ce/gitlab-ce_12.9.0-ce.0_amd64.deb

    # 安装
    $ sudo dpkg -i gitlab-ce_12.9.0-ce.0_amd64.deb
    ...
    Thank you for installing GitLab!
    GitLab was unable to detect a valid hostname for your instance.
    Please configure a URL for your GitLab instance by setting `external_url`
    configuration in /etc/gitlab/gitlab.rb file.
    Then, you can start your GitLab instance by running the following command:
      sudo gitlab-ctl reconfigure
    ...
    # 配置路径：/etc/gitlab/gitlab.rb
    # 重启配置命令：sudo gitlab-ctl reconfigure
    ```

4. 配置

    ```shell
    $ sudo vi /etc/gitlab/gitlab.rb
    # 随意选择一个没有被占用的端口
    external_url 'http://10.1.1.90:8888'
    # 保存后退出
    # 重启配置
    $ sudo gitlab-ctl reconfigure
    ...
    Running handlers:
    Running handlers complete
    Chef Client finished, 9/738 resources updated in 24 seconds
    gitlab Reconfigured!
    $ sudo gitlab-ctl start

    # 开机启动
    $ systemctl enable gitlab-runsvdir.service

    ```

5. 测试

   ```shell
   # 关闭防火墙（懒得添加端口）
   $ sudo ufw disable
   # 打开浏览器登录本机IP测试
   ```
