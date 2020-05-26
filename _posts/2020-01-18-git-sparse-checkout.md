---
layout: mypost
title: git sparse checkout
categories: [tools,git]
---

### git-sparse-checkout

> 因为在下载一些RTOS时会附带很多BSP的其他文件，非常碍眼，所以在 checkout 时，希望可以检出自己需要的文件。

操作系统：Windows 10

Linux系统：WSL Ubuntu 18.04 LTS

Git版本：git version 2.25.0

参考资料：<https://git-scm.com/docs/git-sparse-checkout>

资源链接：<https://gitee.com/rtthread/rt-thread.git>

```shell
$ mkdir rt-thread
$ cd rt-thread/
$ git init
Initialized empty Git repository in /home/ubuntu/rt-thread/.git/
$ git remote add origin https://gitee.com/rtthread/rt-thread.git

# 使能 sparseCheckout 功能
$ git config core.sparseCheckout true

# 生成 .git/info/sparse-checkout 文件
$ git sparse-checkout init

# 查看 .git/info/sparse-checkout 内容
$ git sparse-checkout list
/*
!/*/

# 配置过滤器如下
$ vi .git/info/sparse-checkout

# 从远程仓库更新
$ git pull origin
You asked to pull from the remote 'origin', but did not specify
a branch. Because this is not the default configured remote
for your current branch, you must specify a branch on the command line.

# 选中所需远程分支
$ git pull origin gitee_master
```

.git/info/sparse-checkout 内容：

```git
# 选择所有文件
/*

# 过滤 bsp 目录下的所有文件
!/bsp/*
# 选择 bsp 中的 STM32 目录
/bsp/stm32

# 过滤 componets/vmm 目录
!/components/vmm

# 过滤 example 目录下的所有文件
!/examples/*
# 过滤 documentation 目录下的所有文件
!/documentation/*

# 过滤 libcpu 目录下的所有文件
!/libcpu/*
# 选择 libcpu 目录下的 arm, Kconfig, SConscript
/libcpu/arm
/libcpu/Kconfig
/libcpu/SConscript
```
