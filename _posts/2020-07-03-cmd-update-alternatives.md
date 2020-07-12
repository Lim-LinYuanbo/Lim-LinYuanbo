---
layout: mypost
title: update-alternatives（python版本替换实例）
categories: [运维,ubuntu]
---

## 描述

update-alternatives creates, removes, maintains and displays information about the symbolic links comprising the Debian alternatives system.

## 帮助

```shell
Usage: update-alternatives [<option> ...] <command>

Commands:
  --install <link> <name> <path> <priority>
    [--slave <link> <name> <path>] ...
                           add a group of alternatives to the system.
  --remove <name> <path>   remove <path> from the <name> group alternative.
  --remove-all <name>      remove <name> group from the alternatives system.
  --auto <name>            switch the master link <name> to automatic mode.
  --display <name>         display information about the <name> group.
  --query <name>           machine parseable version of --display <name>.
  --list <name>            display all targets of the <name> group.
  --get-selections         list master alternative names and their status.
  --set-selections         read alternative status from standard input.
  --config <name>          show alternatives for the <name> group and ask the
                           user to select which one to use.
  --set <name> <path>      set <path> as alternative for <name>.
  --all                    call --config on all alternatives.

<link> is the symlink pointing to /etc/alternatives/<name>.
  (e.g. /usr/bin/pager)
<name> is the master name for this link group.
  (e.g. pager)
<path> is the location of one of the alternative target files.
  (e.g. /usr/bin/less)
<priority> is an integer; options with higher numbers have higher priority in
  automatic mode.

Options:
  --altdir <directory>     change the alternatives directory.
  --admindir <directory>   change the administrative directory.
  --log <file>             change the log file.
  --force                  allow replacing files with alternative links.
  --skip-auto              skip prompt for alternatives correctly configured
                           in automatic mode (relevant for --config only)
  --verbose                verbose operation, more output.
  --quiet                  quiet operation, minimal output.
  --help                   show this help message.
  --version                show the version.
```

## 实操

1. 系统状态

    ```shell
    # 这是当前系统发行版
    $ lsb_release -a
    No LSB modules are available.
    Distributor ID: Ubuntu
    Description:    Ubuntu 18.04.3 LTS
    Release:        18.04
    Codename:       bionic

    # 查看 python 版本
    $ python --version
    Python 2.7.17

    # 添加一个更新的 python 版本
    $ sudo apt install python3.8
    # 再次查看 python 版本依然为 2.7
    ```

2. 命令优先级配置

    命令格式：`update-alternatives --install <link> <name> <path> <priority> [--slave <link> <name> <path>]`

    命令说明：`update-alternatives --install <软链接路径> <命令名称> <命令路径> <优先级>`

    ```shell
    # 可以先看看命令 python 是否为软链接
    $ ls -al /usr/bin/python
    lrwxrwxrwx 1 root root 9 Apr 16  2018 /usr/bin/python -> python2.7

    # 配置命令与优先级
    $ sudo update-alternatives --install /usr/bin/python python /usr/bin/python2.7 1
    $ sudo update-alternatives --install /usr/bin/python python /usr/bin/python3.6 2
    $ sudo update-alternatives --install /usr/bin/python python /usr/bin/python3.8 3

    # 查看配置情况
    $ update-alternatives --display python
    python - auto mode
    link best version is /usr/bin/python3.8
    link currently points to /usr/bin/python3.8
    link python is /usr/bin/python
    /usr/bin/python2.7 - priority 1
    /usr/bin/python3.6 - priority 2
    /usr/bin/python3.8 - priority 3

    # 查看当前版本
    $ python --version
    Python 3.8.0
    ```

3. 命令版本切换

    命令格式：`update-alternatives --config <name>`

    命令说明：`update-alternatives --config <指令名称>`

    ```shell
    # 配置版本切换为 python2.7
    $ sudo update-alternatives --config python
    There are 3 choices for the alternative python (providing /usr/bin/python).

    Selection    Path                Priority   Status
    ------------------------------------------------------------
    * 0            /usr/bin/python3.8   3         auto mode
    1            /usr/bin/python2.7   1         manual mode
    2            /usr/bin/python3.6   2         manual mode
    3            /usr/bin/python3.8   3         manual mode

    Press <enter> to keep the current choice[*], or type selection number: 1
    update-alternatives: using /usr/bin/python2.7 to provide /usr/bin/python (python) in manual mode

    # 查看当前版本
    $ python --version
    Python 2.7.17
    ```
