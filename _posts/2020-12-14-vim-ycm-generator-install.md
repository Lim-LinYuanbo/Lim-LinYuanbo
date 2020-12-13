---
layout: mypost
title: vim 插件 YCM-Generator 安装使用记录
categories: [tools,vim]
---

> ```.ycm_extra_conf.py``` 是个头痛的东西，有自动生成的工具就很方便了。

vim-init 项目地址：[https://github.com/Lim-LinYuanbo/vim-init](https://github.com/Lim-LinYuanbo/vim-init)

YCM-Generator 项目地址：[https://github.com/rdnetto/YCM-Generator](https://github.com/rdnetto/YCM-Generator)

安装测试环境：

操作系统：Windows 10

Linux系统：WSL Ubuntu 18.04 LTS

---

## 1. 安装 YCM-Generator

参考：[https://github.com/rdnetto/YCM-Generator#installation](https://github.com/rdnetto/YCM-Generator#installation)

```shell
$ sudo apt install python
$ python2 --version
Python 2.7.17

$ sudo apt install clang
$ clang --version
clang version 6.0.0-1ubuntu2

$ vi .vim/vim-init/init/init-plugins.vim
call plug#begin(get(g:, 'bundle_home', '~/.vim/bundles'))
...
Plug 'rdnetto/YCM-Generator', { 'branch': 'stable'}
...
call plug#end()

$ vi
:PlugInstall
- Finishing ... Done!
...
- YCM-Generator: remote: Total 27 (delta 4), reused 15 (delta 0), pack-reused 0
...
```

## 2. 测试与使用

参考：[https://github.com/rdnetto/YCM-Generator#requirements-and-limitations](https://github.com/rdnetto/YCM-Generator#requirements-and-limitations)

插件依赖 & 支持系统

> * Requirements:
>     + Python 2
>     + Clang
>
> * Supported build systems:
>     + make
>     + cmake
>     + qmake
>     + autotools

测试工程：[https://github.com/HardySimpson/zlog](https://github.com/HardySimpson/zlog)

```shell
$ git clone https://github.com/HardySimpson/zlog.git
$ cd zlog/

# 执行命令生成 .ycm_extra_conf.py
:YcmGenerateConfig

$ ls -al .ycm_extra_conf.py
-rw-r--r-- 1 ubuntu ubuntu  4890 Dec 13 17:05 .ycm_extra_conf.py

$ vi src/buf.c
"src/buf.c" 649L, 17725B
Found /home/ubuntu/zlog/.ycm_extra_conf.py. Load?

(Question can be turned off with options, see YCM docs)
[O]k, (C)ancel:
# 提示是否加载 .ycm_extra_conf.py ，输入 O 完成加载
```

## 3. 其他相关

某些工程没有 `make` 或 `cmake` 等，使用模板的或者他人提供 `.ycm_extra_conf.py` 的？

目前似乎没有找到什么比较合适的办法。

```shell
$ find .vim/bundles/YouCompleteMe/ -name ".ycm_extra_conf.py"
.vim/bundles/YouCompleteMe/third_party/ycmd/ycmd/tests/clang/testdata/.ycm_extra_conf.py
.vim/bundles/YouCompleteMe/third_party/ycmd/ycmd/tests/clang/testdata/client_data/.ycm_extra_conf.py
.vim/bundles/YouCompleteMe/third_party/ycmd/ycmd/tests/clang/testdata/driver_mode_cl/executable/.ycm_extra_conf.py
.vim/bundles/YouCompleteMe/third_party/ycmd/ycmd/tests/clang/testdata/driver_mode_cl/flag/.ycm_extra_conf.py
.vim/bundles/YouCompleteMe/third_party/ycmd/ycmd/tests/clang/testdata/general_fallback/.ycm_extra_conf.py
.vim/bundles/YouCompleteMe/third_party/ycmd/ycmd/tests/clang/testdata/get_doc/.ycm_extra_conf.py
.vim/bundles/YouCompleteMe/third_party/ycmd/ycmd/tests/clang/testdata/noflags/.ycm_extra_conf.py
.vim/bundles/YouCompleteMe/third_party/ycmd/ycmd/tests/clang/testdata/test-include/.ycm_extra_conf.py
.vim/bundles/YouCompleteMe/third_party/ycmd/ycmd/tests/clangd/testdata/extra_conf/.ycm_extra_conf.py
```
