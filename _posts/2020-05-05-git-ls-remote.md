---
layout: mypost
title: git 常用基础操作
categories: [运维,git]
---

### git-ls-remote

> 在使用 Jenkins 的时候看到这个玩意，所以必须了解一下。

参考资料：<https://git-scm.com/docs/git-ls-remote>

```shell
$ git ls-remote -h
usage: git ls-remote [--heads] [--tags] [--refs] [--upload-pack=<exec>]
                     [-q | --quiet] [--exit-code] [--get-url]
                     [--symref] [<repository> [<refs>...]]

    -q, --quiet           do not print remote URL
    --upload-pack <exec>  path of git-upload-pack on the remote host
    -t, --tags            limit to tags
    -h, --heads           limit to heads
    --refs                do not show peeled tags
    --get-url             take url.<base>.insteadOf into account
    --sort <key>          field name to sort on
    --exit-code           exit with exit code 2 if no matching refs are found
    --symref              show underlying ref in addition to the object pointed by it
    -o, --server-option <server-specific>
                          option to transmit

# 查看远程仓库分支？？？
$ git ls-remote -h repository
# 查看远程仓库 tag
$ git ls-remote -t repository
# refs = heads + tags
$ git ls-remote --refs repository
# 其他的似乎我还用不上
```
