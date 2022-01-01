---
#layout: mypost
title: vim 学习笔记
categories:
- [vim]
tags:
- vim
---

> 为什么我要用 VIM ？是的，第一，我喜欢装逼；第二， VIM 确实非常好用可以完全把双手放在键盘之上，把鼠标丢进垃圾桶。
>
> 个人学习总结，因为以前我使用 VIM 至少在单个文本上编辑的，在项目工程上都是使用 source insight ，项目工程上一般会有很多文件直接的关联，所以我觉得可以先使用 VIM 阅读代码，然后再进入编辑阶段。

我的工作环境：

操作系统：Windows 10

Linux系统：WSL Ubuntu 18.04 LTS

Vim版本: VIM - Vi IMproved 8.0

仓库地址：[vim-init](https://github.com/Lim-LinYuanbo/vim-init)

### 代码阅读篇

#### 从 source insight 到 vim

1. 搭建属于自己的环境，出于偷懒，可以直接使用 [vim-init](https://github.com/skywind3000/vim-init) 快速完成这个过程。

    根据 vim-init 的文档，安装过程如下：

    ```shell
    # 创建 .vim 文件夹 与 .vimrc 文件
    $ mkdir .vim
    $ touch .vimrc

    # 克隆工程到本地
    $ cd .vim/
    /.vim$ git clone https://github.com/Lim-LinYuanbo/vim-init
    Cloning into 'vim-init'...
    remote: Enumerating objects: 14, done.
    remote: Counting objects: 100% (14/14), done.
    remote: Compressing objects: 100% (11/11), done.
    remote: Total 400 (delta 7), reused 9 (delta 3), pack-reused 386
    Receiving objects: 100% (400/400), 257.20 KiB | 345.00 KiB/s, done.
    Resolving deltas: 100% (235/235), done.

    # 添加如下配置
    $ vi ~/.vimrc
    source ~/.vim/vim-init/init.vim

    # 启动 vi 运行 :PlugInstall
    # 提示安装完成：Finishing ... Done!
    ```

2. 额外需要安装的插件，如下：

    ~~[ctags](https://ctags.io/) 上古时期的还是挺好用的，安装指令：`sudo apt-get install exuberant-ctags`。~~

    ~~[cscope](http://cscope.sourceforge.net/) 同样感觉是上古时期的工具，安装指令：`sudo apt-get install cscope`。~~

    [global](https://www.gnu.org/software/global/) 使用[gutentags](https://github.com/ludovicchabant/vim-gutentags)似乎需要安装global这个玩意，直接 `sudo apt-get install global` 安装即可。

3. source insight 新建工程，同步代码阶段。

    以 [RT-Thread](https://github.com/RT-Thread/rt-thread) 源码工程为例，在工程目录，这是一个 git 目录，无需创建 .root 文件。

    > 补充：假如如果使用 .git 所处层级的源码太多，那么可以在指定曾经创建一个 .root 来指定项目根目录（已经实测，感觉没有毛病）
    >
    > 参考：[https://github.com/skywind3000/vim-init/blob/master/init/init-plugins.vim](https://github.com/skywind3000/vim-init/blob/master/init/init-plugins.vim)
    >
    > "----------------------------------------------------------------------
    >
    > " 自动生成 ctags/gtags，并提供自动索引功能
    >
    > " 不在 git/svn 内的项目，需要在项目根目录 touch 一个空的 .root 文件
    >
    > " 详细用法见：<https://zhuanlan.zhihu.com/p/36279445>
    >
    > "----------------------------------------------------------------------

    进入项目文件并使用 vi 打开文件之后，就会创建上述索引缓冲区 `~/.cache/tags/home-ubuntu-rt-thread`

4. 文件树菜单，该功能类似 source insight 的 Project Folder Browser 。

    > 具体配置参考：[init-plugins.vim](https://github.com/skywind3000/vim-init/blob/master/init/init-plugins.vim)
    >
    > 使用 [nerdtree](https://github.com/preservim/nerdtree) 打开文件
    >
    > 1.在源码根目录下触发 `vi .` 就可以直接触发
    >
    > 2.在 vi 下普通模式，按下 `space nt`(`space nt` 默认配置按键) 或 运行 `:NERDTree`
    >
    > 3.在 NERDTree 窗口中，按下 `o` 可以打开文件并将光标跳转到文件窗口，按下 `go` 则为打开文件但光标不跳转到文件窗口
    >
    > 4.在 NERDTree 窗口中，按下 `t` 或 `T` 可以以新标签形式打开文件，`t` 与 `T` 的区别类似 `o` 与 `go`
    >
    > 5.基于第4点，打开多个标签可以使用 `alt+数字键` 切换标签，具体按键配置参考：[init-keymaps.vim](https://github.com/skywind3000/vim-init/blob/master/init/init-keymaps.vim)

5. 文件模糊查找，该功能类似 source insight 的 Project File List 。

    > 具体配置参考：[init-plugins.vim](https://github.com/skywind3000/vim-init/blob/master/init/init-plugins.vim)
    >
    >"----------------------------------------------------------------------
    >
    >" LeaderF：CtrlP / FZF 的超级代替者，文件模糊匹配，tags/函数名 选择
    >
    >"----------------------------------------------------------------------
    >
    > 默认启动方式与常用功能：
    >
    > 1. 在 vi 下普通模式 `<c-p>` 触发，输入文件名后回车确认
    >
    > 2. 在模糊查找窗口，使用 `<c-j>` 和 `<c-k>` 进行文件上下选择

6. 函数列表，该功能类似 source insight 的 Symbol Window 。

    > 具体配置参考：[init-plugins.vim](https://github.com/skywind3000/vim-init/blob/master/init/init-plugins.vim)
    >
    > " ALT+p 打开函数列表，按 i 进入模糊匹配，ESC 退出
    >
    > `noremap <m-p> :LeaderfFunction!<cr>`

7. 代码阅读之跳转

    > 具体配置参考：[init-plugins.vim](https://github.com/skywind3000/vim-init/blob/master/init/init-plugins.vim)
    > [gutentags_plus](https://github.com/skywind3000/gutentags_plus)
    >
    > 使用 `<leader>cs` 搜索之后底部弹窗 Preview，选中之后：
    >
    > 1.回车打开；
    >
    > 2.小写p打开新窗口预览；
    >
    > 3.大写P关闭窗口预览；

8. 搜索

---

### 代码编写篇

#### 第一阶段：参考 [简明 VIM 练级攻略](https://coolshell.cn/articles/5426.html) （耗子叔的这篇文章真的很棒）

#### 第二阶段：使用 ~~spf13-vim~~ 中的插件提升编码效率

1. 注释

    [nerdcommenter](https://github.com/preservim/nerdcommenter) 常用的使用方法：...，效果如图：

2. 自动补全

   [neocomplete](https://github.com/Shougo/neocomplete.vim) spf13-vim 默认使用的插件

   [youcompleteme](https://github.com/ycm-core/YouCompleteMe) google开发的工具，安装有点麻烦

   至于两个工具的比较，网上已经铺天盖地，效果如下：

   贴个动图...

### 遇到的问题

#### YouCompleteMe

1. 使用 vi 打开 .c 文件之后提示：

   ```shell
   NoExtraConfDetected: No .ycm_extra_conf.py file detected, so no compile flags are available. Thus no semantic suppor...
   ```

    通过查阅网上资料与官方说明：

    1. 在官方说明 `C-family Semantic Completion` 章节有说到与 `.ycm_extra_conf.py` 文件相关的说法。

    2. 目前网上一些解决方案应该使用的是 `Option 2: Provide the flags manually`。

    以下是我参考网上的做法与我的理解，解决该问题步骤如下：

    1. 首先创建 `.ycm_extra_conf.py` 文件（文件位置自定义）

    2. 填写 `.ycm_extra_conf.py` 文件内容，参考 [issues](https://github.com/ycm-core/YouCompleteMe/issues/2249)

    3. 在你的 `.vimrc` 中使用 `let g:ycm_global_ycm_extra_conf = xxx` 指定你的 `.ycm_extra_conf.py` 路径
