---
#layout: mypost
title: Jenkins 学习笔记
categories:
- [运维]
tags:
- 运维
- jenkins
---

> 参考：[https://www.jenkins.io/zh/doc/book/installing/](https://www.jenkins.io/zh/doc/book/installing/)
>
> 参考：[https://jenkins-zh.cn/](https://jenkins-zh.cn/)
>
> 优秀的学习资源：[http://www.eryajf.net/](http://www.eryajf.net/)
>
> 系统版本：ubuntu 18.04 LTS

### 安装记录

1. 安装程序

        ```shell
        # 安装依赖
        $ sudo apt install default-jdk
        $ sudo apt install openjdk-8-jdk
        # 添加环境变量
        $ ls /usr/lib/jvm/java-8-openjdk-amd64/
        $ sudo vi /etc/profile
        ...
        export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
        export PATH=$PATH:$JAVA_HOME/bin
        ...
        $ source /etc/profile

        # 下载安装包
        $ wget https://mirrors.tuna.tsinghua.edu.cn/jenkins/debian-stable/jenkins_2.222.1_all.deb

        # 安装
        $ sudo dpkg -i jenkins_2.222.1_all.deb
        dpkg: dependency problems prevent configuration of jenkins:
         jenkins depends on daemon; however:
          Package daemon is not installed. # 这是安装过程提示缺少依赖的提示
        # 安装 daemon 之后再次安装
        $ sudo apt install daemon
        $ sudo dpkg -i jenkins_2.222.1_all.deb
        (Reading database ... 134208 files and directories currently installed.)
        Preparing to unpack jenkins_2.222.1_all.deb ...
        Unpacking jenkins (2.222.1) over (2.222.1) ...
        Setting up jenkins (2.222.1) ...
        Processing triggers for ureadahead (0.100.0-21) ...
        Processing triggers for systemd (237-3ubuntu10.33) ...
        # 安装完成

        # 查看默认端口（同时修改为未使用的端口）
        $ sudo vi /etc/default/jenkins
        ...
        # port for HTTP connector (default 8080; disable with -1)
        HTTP_PORT=9999 # 默认端口
        
        # 启动 Jenkins
        $ sudo systemctl restart jenkins.service
        # 查看 端口是否处于监听状态
        $ netstat -aut
        # 没有监听就重启一下吧！
        # 打开浏览器访问确认
        ```

2. 通用配置

    1) 替换插件更新源（在线安装）：

        > 参考：[https://jenkins-zh.cn/tutorial/management/plugin/update-center/](https://jenkins-zh.cn/tutorial/management/plugin/update-center/)

        网页操作：Jenkins -> Manage Jenkins -> Plugin Manager -> Advanced

        替换 `Update Site` 网站为 `https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json` 后提交

            ```shell
            # 重启 Jenkins
            $ sudo systemctl restart jenkins.service

            # 替换（似乎还是要替换这些才能快速下载插件）
            $ cd /var/lib/jenkins/updates/
            $ sudo sed -i 's/http:\/\/updates.jenkins-ci.org\/download/https:\/\/mirrors.tuna.tsinghua.edu.cn\/jenkins/g' default.json
            $ sudo sed -i 's/http:\/\/www.google.com/https:\/\/www.baidu.com/g' default.json
            $ sudo systemctl restart jenkins.service
            ```

        > 备注：感觉即使替换也不能 100% 快速下载插件，有时还需要尝试多个时间的更新下载。

    2) 本地上传插件（离线安装）：

        即使已经替换国内源，但是有时还是没法下载，所以有时还是需要使用离线安装功能。

        插件下载地址：[https://mirrors.tuna.tsinghua.edu.cn/jenkins/plugins/](https://mirrors.tuna.tsinghua.edu.cn/jenkins/plugins/)

    3) 常用插件安装

        Localization: Chinese (Simplified)：Jenkins Core 及其插件的简体中文语言包，由 Jenkins 中文社区维护。

        Role-based Authorization Strategy：给Jenkins用户权限管理添加了角色组。

        Credentials Binding：凭证相关

        Pipeline：添加一个新的Job类型：Pipeline。

        Git：该插件允许使用GIT作为一个构建SCM(源代码控制管理系统)。

        Git Parameter：该插件可以扩展参数化构建过程。

3. 安装过程遇到问题

    1) 一直卡在 `Please wait while Jenkins is getting ready to work` 页面

        解决：将 `hudson.model.UpdateCenter.xml` 文件的 URL 替换为 `https://mirrors.tuna.tsinghua.edu.cn/jenkins/updates/update-center.json` 后，重启服务。

### 插件与使用

1. Git/Credentials Plugin

        ```shell
        # 修改 jenkins 密码
        $ sudo passwd jenkins
        Enter new UNIX password:
        Retype new UNIX password:
        passwd: password updated successfully
        # 切换 jenkins 用户
        $ su jenkins
        Password:
        
        # 生成 ssh 公钥&私钥
        $ cd ~
        $ ssh-keygen -t rsa -C "jenkins@gmail.com"
        $ cat ~/.ssh/id_rsa.pub
        $ cat ~/.ssh/id_rsa
        # 配置 git 用户信息
        $ git config --global user.name "vm-jenkins"
        $ git config --global user.email "vm-jenkins@gmail.com"
        $ cat ~/.gitconfig
        [user]
                name = vm-jenkins
                email = vm-jenkins@gmail.com
        
        # 将公钥放于 git 服务器用于测试克隆 ssh url 是否成功
        $ git clone user@ssl_url
        # 成功克隆之后，上 jenkins 添加私钥与构建任务测试是否成功
        
        # 将公钥放于 git 服务器用于测试克隆 https url 是否成功
        $ git clone https_url
        # 成功克隆之后，上 jenkins 添加私钥与构建任务测试是否成功
        ```

### 构建

1. Trigger builds remotely（触发远程构建）

    > 如果没有这个选项似乎是安全配置项有些地方没配置？？？

    1) 生成身份验证令牌

        jenkins->用户列表->管理该项目用户（我是这样填的...）->设置->API Token->项目名（我是这样填的...）->生成->复制

    2) 配置项目

        jenkins->目标项目->配置->构建触发器->触发远程构建->身份验证令牌（刚刚生成那串）

    3) 访问测试

        根据刚刚的提示，生成的URL为：`JENKINS_URL/job/local-test-ssh/build?token=TOKEN_NAME`。

        尝试访问后发现似乎需要登录账号？？？通过搜索和尝试，修改权限可以解决。

    4) 修改权限

        Jenkins->系统管理->全局安全设置->授权策略->项目矩阵授权策略->Anonymous Users->全部（read）/任务（read）

    5) 再次尝试

        同时打开构建记录看看，问题不大，成功！

2. Generic Webhook Trigger

    > 参考：[https://plugins.jenkins.io/generic-webhook-trigger/](https://plugins.jenkins.io/generic-webhook-trigger/)

    1) 安装 Generic Webhook Trigge 插件

    2) 工作项目配置（jenkins 页面操作）

        配置->Generic Webhook Trigger->Token->填写同触发远程构建->保存

        > 注意：必须把“触发远程构建”的勾选给去掉，不然无法触发 Generic Webhook Trigger

    3) 测试（触发远程构建的设备）

            ```shell
            # 安装 curl
            $ sudo apt install curl

            # 使用 curl 模拟 post 操作触发
            $ curl -vs http://JENKINS_URL/generic-webhook-trigger/invoke?token=TOKEN_NAME 2>&1

            # 查看是否成功，问题不大，成功！
            ```

    4) Post content parameters

    5) Header parameters

    6) Request parameters

    7) Override Quiet Period
