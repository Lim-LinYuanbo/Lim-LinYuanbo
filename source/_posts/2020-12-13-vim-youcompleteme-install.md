---
#layout: mypost
title: vim 插件 YouCompleteMe 安装记录
categories:
- [vim]
tags:
- vim
---

> 这个插件依赖的东西挺多，而且经常遇到问题，所以重新安装一次还是需要手动记录一下。

vim-init 项目地址：[https://github.com/Lim-LinYuanbo/vim-init](https://github.com/Lim-LinYuanbo/vim-init)

YouCompleteMe 项目地址：[https://github.com/ycm-core/YouCompleteMe](https://github.com/ycm-core/YouCompleteMe)

安装测试环境：

操作系统：Windows 10

Linux系统：WSL Ubuntu 18.04 LTS

---

## 1. 安装依赖程序

最小系统要求：[https://github.com/ycm-core/YouCompleteMe#installation](https://github.com/ycm-core/YouCompleteMe#installation)

> Requirements
> Minimum supported versions:
>
> - Vim v8.1.2269 huge build, compiled with Python 3.6 support (aka `vim-nox` in Ubuntu 20.04 LTS)
> - Python 3.6 runtime, compiled with `--enable-shared` (or `--enable-framework`)
> Please note that some features are not availble in Neovim, and Neovim is not
officially supported.

```shell
$ sudo apt install python3 python3-dev
$ python3 --version
Python 3.6.9

$ sudo apt install vim
$ vim --version
VIM - Vi IMproved 8.0 (2016 Sep 12, compiled Oct 13 2020 15:49:09)
# 该版本过旧，需要更新最新版本
$ sudo add-apt-repository ppa:jonathonf/vim
$ sudo apt update
# 该过程及其缓慢，使用代理也是慢，不知为何
# 中间还失败了几次???多尝试几次似乎解决了
$ sudo apt install vim
$ vim --version
VIM - Vi IMproved 8.2 (2019 Dec 12, compiled Oct 24 2020 16:26:37)
```

Ubuntu依赖程序：[https://github.com/ycm-core/YouCompleteMe#linux-64-bit](https://github.com/ycm-core/YouCompleteMe#linux-64-bit)

> - Ubuntu 16.04 and later:
>
>   sudo apt install build-essential cmake python3-dev

```shell
$ sudo apt install build-essential cmake python3-dev
...
```

## 2. 使用插件安装 YouCompleteMe

> 在解决 VIM 8.0 无法使用 YouCompleteMe 的过程有人提出使用如下方式安装，并且指定使用 COMMIT ID 的 YouCompleteMe 版本

```shell
# 在插件安装栏中添加 Plug 'Valloric/YouCompleteMe', { 'commit':'d98f896' }
# 指定选择 commit id，否则执行安装的时候要支持更高的 C++ 版本
$ vi .vim/vim-init/init/init-plugins.vim
call plug#begin(get(g:, 'bundle_home', '~/.vim/bundles'))
...
Plug 'Valloric/YouCompleteMe', { 'commit':'d98f896' }
...
call plug#end()


# 使用代理打开 Vim
$ proxychains vim
# 执行安装功能，如果没有使用代理，会非常慢 :(
:PlugInstall
Finishing ... Done!
...
- YouCompleteMe: HEAD is now at d98f896a ...
...
# 提示如下，通过运行 :YcmRestartServer 可以发现是说没有编译。
The ycmd server SHUT DOWN (restart with ':YcmRestartServer'). Unexpected exit code 0. Type ':YcmToggleLogs ycmd_53628_stderr_a59kolj8.log' to check the logs.


# 编译步骤，我只使用 C语言 所以添加 --clangd-completer 就行
$ cd .vim/bundles/YouCompleteMe/
$ python3 install.py --clangd-completer
Done installing Clangd
Clangd completer enabled. If you are using .ycm_extra_conf.py files, make sure they use Settings() instead of the old and deprecated FlagsForFile().
```

## 测试使用效果

1. 关于 ```.ycm_extra_conf.py``` 快速解决参考：[https://github.com/ycm-core/YouCompleteMe/issues/2249](https://github.com/ycm-core/YouCompleteMe/issues/2249)

2. 自动生成 ```.ycm_extra_conf.py``` 参考：[YCM-Generator](https://github.com/rdnetto/YCM-Generator)
