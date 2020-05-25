---
layout: mypost
title: ubuntu 用户操作
categories: [运维,ubuntu]
---

### 添加用户常用步骤

1. 添加用户：`useradd -d /home/user_name -m -s /bin/bash user_name`

    > -d：指定用户登入时的起始目录
    >
    > -m：自动建立用户的登入目录
    >
    > -s：指定用户登入后所使用的shell
    >
    > -p：设定用户登入密码（但是这里的密码是密文，所以没有使用）

2. 修改密码：`passwd user_name`

    ```shell
    Enter new UNIX password:
    Retype new UNIX password:
    passwd: password updated successfully
    ```

3. 添加权限

    sudo权限编辑路径：`/etc/sudoers`

    ```shell
    # User privilege specification
    ...
    user_name ALL=(ALL:ALL) ALL
    ...
    ```

### 删除用户常用步骤（添加的逆操作）

1. 删除用户：`userdel -r user_name`

    > -r：删除用户登入目录以及目录中所有文件

2. 删除权限配置文件

    sudo权限编辑路径：`/etc/sudoers`
