---
#layout: mypost
title: 镜像站相关
categories:
- [运维]
tags:
- 运维
---

### [清华大学开源软件镜像](https://mirrors.tuna.tsinghua.edu.cn/)

实例：

> 系统版本：ubuntu 18.04 LTS

1. 相关链接 > 使用帮助

2. 选择 ubuntu ，选择系统版本

#### 下载软件

实例：

> 系统版本：ubuntu 18.04 LTS
>
> 目标：GitLab

1. 搜索 GitLab ，发现 CE与CC 版本区别？[gitlab-ce or gitlab-cc](https://about.gitlab.com/install/ce-or-ee/)

2. 目录路径：`/gitlab-ee/ubuntu/pool/bionic/main/g/gitlab-ee`，dists与pool 是啥？dists存放的是软件包数据，pool存放具体应用的软件包

### [阿里开源镜像](https://developer.aliyun.com/mirror/)

1. 替换更新源

    实例：

    > 系统版本：ubuntu 18.04 LTS

   1. 开发者社区

   2. 镜像 > 系统 > ubuntu

2. 下载软件

    实例：

    > 系统版本：ubuntu 18.04 LTS
    >
    > 目标：GitLab

   1. 搜索 GitLab

   2. 仓库名称：各种发行版名称，选择 ubuntu

   3. 发行版：版本号，选择 bionic

   4. 架构：x86/x64，选择 amd64
