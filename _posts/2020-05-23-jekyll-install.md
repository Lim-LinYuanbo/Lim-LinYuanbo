---
layout: mypost
title: jekyll 安装笔记
categories: [运维,jekyll]
---

参考：[jekyllrb.com/docs](https://jekyllrb.com/docs/)

## 安装笔记

1. 安装

    ```shell
    # 安装依赖
    $ sudo apt install ruby-full build-essential zlib1g-dev

    # 设置 gem 安装目录与命令路径
    $ echo '# Install Ruby Gems to ~/gems' >> ~/.bashrc
    $ echo 'export GEM_HOME="$HOME/gems"' >> ~/.bashrc
    $ echo 'export PATH="$HOME/gems/bin:$PATH"' >> ~/.bashrc
    $ source ~/.bashrc

    # 替换源
    $ gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/

    # 安装 jekyll 和 bundler
    $ gem update
    $ gem install jekyll bundler
    ```

2. 测试

    ```shell
    # 添加测试项目
    $ jekyll new test
    $ cd test
    $ jekyll serve

    # 使用 curl 测试是否可以访问
    $ curl http://localhost:4000/
    ```

## 其他
