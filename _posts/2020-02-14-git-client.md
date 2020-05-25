---
layout: mypost
title: git client 基础操作
categories: [tools,git]
---

### 客户端操作

> 参考连接：[https://git-scm.com/book/en/v2](https://git-scm.com/book/en/v2)
>
> 参考连接：[https://www.liaoxuefeng.com/wiki/896043488029600](https://www.liaoxuefeng.com/wiki/896043488029600)

1. 工作环境配置

    工作环境：Windows 10

    安装 Git-64-bit.exe 链接：[https://git-scm.com/download/win](https://git-scm.com/download/win)

    配置个人信息以及生成ssh密钥：

    ```shell
    # 配置用户名
    $ git config --global user.name "superman"
    # 配置用户邮箱
    $ git config --global user.email "superman@gmail.com"
    # 生成ssh密钥
    $ ssh-keygen.exe -t rsa -C "superman@gmail.com"
    Generating public/private rsa key pair.
    Enter file in which to save the key (/c/Users/Administrator/.ssh/id_rsa):
    Created directory '/c/Users/Administrator/.ssh'.
    Enter passphrase (empty for no passphrase):
    Enter same passphrase again:
    Your identification has been saved in /c/Users/Administrator/.ssh/id_rsa.
    Your public key has been saved in /c/Users/Administrator/.ssh/id_rsa.pub.
    The key fingerprint is:
    SHA256:XCaLfu3ooJjudPwsMqzPiYRAavGYKu1FDcWlE3ejcVE superman@gmail.com
    The key's randomart image is:
    +---[RSA 3072]----+
    |     .o.+ =oE    |
    |     ..+ = .     |
    | o  . o o o      |
    |o =  o + =       |
    |o+ .. o S        |
    |=. o .   .       |
    |+oo + o . .      |
    |o=+*.+ o o       |
    |o=Xo..o.o .      |
    +----[SHA256]-----+
    # 查看公钥，并将你的公钥提供给 git 仓库管理员
    $ cat ~/.ssh/id_rsa.pub
    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQDTLVL3+iGf6cQRBIX3TdVOGjSaYGjcygxmgSIJ2Bek99+/h91RWXpJAPckqv0Gsx+txh8rLiPCI7Q3ap3VjkAN1facX7qoWPJ1D4Dx0QqGzlFyOfIDDf0wwmarkc+ZgDMwUUKqSZ8TZgDxD7dDeK9ng7x6nTR7FFpBbilBW4S3Tn4Dtz9Qd+tp4bY9TJjh7S4cLrMvLEqc6VirLtez6JZFgaZcI9GC8a2avtt9bCZX/gY1YMU9ZFvRRI2cA3tf5O4xx1lfIQDNi+G5VT/wNT1LJhkGtzA+2FjFeBP4vhJhgBN5j25i657ZPRBMID9JLqiShPSMeMZ7aU2vMArEDKnm1sya1cbtJFOM5iHnEhrWS91zxSfIyACK+g/8WH1ABCXKgmlpxgec2bNGTQ7RJep19SpV6Ko6gfjk54w+dzn4E+PX3pGYV827Iv84v27+8A1h0b0APXs42tiXI923INMgJ3H44dR38pI6yb9NT4FX8byViLMsQXUg1R5fpOOGmwM= superman@gmail.com
    ```

2. 远程仓库的管理与克隆

    ```shell
    # 仓库地址由管理员提供
    $ git clone git@106.13.211.199:/home/git/test.git
    Cloning into 'test'...
    warning: You appear to have cloned an empty repository.

    # 进入仓库
    $ cd test/

    # 查看仓库文件列表（目前这是一个空仓库）
    $ ls -al
    total 8
    drwxr-xr-x 1 Administrator 197121 0  2月 19 16:04 ./
    drwxr-xr-x 1 Administrator 197121 0  2月 19 16:04 ../
    drwxr-xr-x 1 Administrator 197121 0  2月 19 16:04 .git/  # 管理git仓库的文件夹

    # 查看 "git remote" 帮助（所有的 git 命令都可以使用 -h 选项查看）
    $ git remote -h
    usage: git remote [-v | --verbose]
        or: git remote add [-t <branch>] [-m <master>] [-f] [--tags | --no-tags] [--mirror=<fetch|push>] <name> <url>
        or: git remote rename <old> <new>
        or: git remote remove <name>
        or: git remote set-head <name> (-a | --auto | -d | --delete | <branch>)
        or: git remote [-v | --verbose] show [-n] <name>
        or: git remote prune [-n | --dry-run] <name>
        or: git remote [-v | --verbose] update [-p | --prune] [(<group> | <remote>)...]
        or: git remote set-branches [--add] <name> <branch>...
        or: git remote get-url [--push] [--all] <name>
        or: git remote set-url [--push] <name> <newurl> [<oldurl>]
        or: git remote set-url --add <name> <newurl>
        or: git remote set-url --delete <name> <url>

        -v, --verbose         be verbose; must be placed before a subcommand

    # 查看远程仓库分支情况
    $ git remote
    origin

    # 查看远程仓库详细信息
    $ git remote -v
    origin  git@106.13.211.199:/home/git/test.git (fetch)   # 拉取地址，即：git pull的地址
    origin  git@106.13.211.199:/home/git/test.git (push)    # 推送地址，即：git push的地址

    # 假设此时远程仓库有更新，本地需要更新与远程仓库保持同步
    $ git pull
    remote: Counting objects: 3, done.
    remote: Total 3 (delta 0), reused 0 (delta 0)
    Unpacking objects: 100% (3/3), 221 bytes | 0 bytes/s, done.
    From 106.13.211.199:/home/git/test
    * [new branch]      master     -> origin/master

    $ ls -al
    total 9
    drwxr-xr-x 1 Administrator 197121  0  2月 19 16:21 ./
    drwxr-xr-x 1 Administrator 197121  0  2月 19 16:04 ../
    drwxr-xr-x 1 Administrator 197121  0  2月 19 16:21 .git/
    -rw-r--r-- 1 Administrator 197121 31  2月 19 16:21 aaa

    # 简单修改文件（实际不建议直接修改master分支的内容）
    $ echo "hello batman, I am superman" >> aaa
    # 将变更文件添加到暂存区
    $ git add aaa
    warning: LF will be replaced by CRLF in aaa.
    The file will have its original line endings in your working directory
    # 提交暂存区的文件并添加简单的提交信息（实际使用不建议使用"git commit -m"，因为不能格式化输入提交信息）
    $ git commit -m "2nd commit:say hello to batman"
    [master ebf69e2] 2nd commit:say hello to batman
    1 file changed, 1 insertion(+)
    # 将本地更新推送到远程仓库
    $ git push origin
    Enumerating objects: 5, done.
    Counting objects: 100% (5/5), done.
    Delta compression using up to 4 threads
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (3/3), 292 bytes | 146.00 KiB/s, done.
    Total 3 (delta 0), reused 0 (delta 0)
    To 106.13.211.199:/home/git/test.git
    ba6225d..ebf69e2  master -> master
    ```

3. 本地版本控制与基础操作练手

    练手仓库路径：`E:\local`

    git 仓库初始化/状态/添加/提交

    ```shell
    # 仓库初始化
    $ git init
    Initialized empty Git repository in E:/local/.git/

    # 查看仓库文件列表（目前这是一个空仓库）
    $ ls -al
    total 8
    drwxr-xr-x 1 Administrator 197121 0  2月 19 17:04 ./
    drwxr-xr-x 1 Administrator 197121 0  2月 19 17:03 ../
    drwxr-xr-x 1 Administrator 197121 0  2月 19 17:04 .git/

    # 查看仓库状态
    $ git status
    On branch master
    No commits yet
    nothing to commit (create/copy files and use "git add" to track)

    # 在该目录下新建一个文件之后，在其添加如下信息：
    # “create new file, file name:test.txt --- 1st commit”
    # 查看仓库状态
    $ git status
    On branch master
    No commits yet
    Untracked files: # 提示未追踪的文件，即该文件未在版本库中
    (use "git add <file>..." to include in what will be committed) # 支持命令的提示
            test.txt
    nothing added to commit but untracked files present (use "git add" to track)

    # 把文件添加到暂存区
    $ git add test.txt

    $ git status
    On branch master
    No commits yet
    Changes to be committed: # 提示改变但并未提交的文件
    (use "git rm --cached <file>..." to unstage) # 提示可以使用该命令将其从暂存区删除
            new file:   test.txt

    # 提交暂存区内容
    $ git commit
    # 以下为 vi编辑器 方式呈现，需要自行了解 vi编辑器 基础操作
    [feature] 1st commit
    [what] test local git test
    [why] nowhy
    [how]

    # Please enter the commit message for your changes. Lines starting
    # with '#' will be ignored, and an empty message aborts the commit.
    #
    # On branch master
    #
    # Initial commit
    #
    # Changes to be committed:
    #       new file:   test.txt
    #

    [master (root-commit) bc42794] [feature] 1st commit [what] test local git test [why] nowhy [how]
    1 file changed, 1 insertion(+)
    create mode 100644 test.txt
    ```

    git 比较/撤销/恢复

    ```shell
    # 再次修改 test.txt 文件，添加如下内容：
    # “test for version control --- 2st commit”
    $ git status
    On branch master
    Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git restore <file>..." to discard changes in working directory)
            modified:   test.txt # 工作区与暂存器发生差异修改的文件

    # 查看文件差异
    $ git diff test.txt
    diff --git a/test.txt b/test.txt
    index 11c6076..18e7cd8 100644
    --- a/test.txt
    +++ b/test.txt
    @@ -1 +1,3 @@
    -create new file, file name:test.txt --- 1st commit # '-' 为旧版本内容
    \ No newline at end of file
    +create new file, file name:test.txt --- 1st commit # '+' 为新版本内容
    +
    +test for version control --- 2st commit
    \ No newline at end of file

    # 此时再次创建一个新的文件，文件名：2nd.txt，内容如下：
    # “create new file, file name:2nd.txt --- 2st commit”
    $ git status
    On branch master
    Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git restore <file>..." to discard changes in working directory)
            modified:   test.txt
    Untracked files:
    (use "git add <file>..." to include in what will be committed)
            2nd.txt
    no changes added to commit (use "git add" and/or "git commit -a")

    # 假设此时觉得 test.txt 的修改是多余的，现在需要撤销
    $ git checkout -- test.txt
    # 查看侧向后文件内容
    $ cat test.txt
    create new file, file name:test.txt --- 1st commit
    # 查看仓库状态
    $ git status
    On branch master
    Untracked files:
    (use "git add <file>..." to include in what will be committed)
            2nd.txt
    nothing added to commit but untracked files present (use "git add" to track)
    # 此时工作去 test.txt 文件已经被还原

    # 再次修改 test.txt 添加如下内容并查看仓库状态：
    # "test for git reset"
    $ cat test.txt
    create new file, file name:test.txt --- 1st commit
    test for git reset
    $ git status
    On branch master
    Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git restore <file>..." to discard changes in working directory)
            modified:   test.txt
    Untracked files:
    (use "git add <file>..." to include in what will be committed)
            2nd.txt
    no changes added to commit (use "git add" and/or "git commit -a")
    # 此时眼疾手快的添加到暂存区并查看仓库状态
    $ git add test.txt 2nd.txt
    $ git status
    On branch master
    Changes to be committed:
    (use "git restore --staged <file>..." to unstage)
            new file:   2nd.txt
            modified:   test.txt
    # 变更的内容已经添加到暂存区了，发现这两个修改不完全，还需要补充，需要还原到工作区
    # 根据提示，可以使用 "git restore" 还原，在旧版本 git 可以使用 "git reset HEAD" 恢复
    $ git restore --staged test.txt 2nd.txt
    $ git status
    On branch master
    Changes not staged for commit:
    (use "git add <file>..." to update what will be committed)
    (use "git restore <file>..." to discard changes in working directory)
            modified:   test.txt
    Untracked files:
    (use "git add <file>..." to include in what will be committed)
            2nd.txt
    no changes added to commit (use "git add" and/or "git commit -a")
    # 再次修改 test.txt 后提交，此时内如如下：
    $ cat test.txt
    create new file, file name:test.txt --- 1st commit
    test for git reset --- 2st commit
    $ git add test.txt 2nd.txt
    $ git commit
    [master 1aba0bc] test git command:git diff/checkout/restore --- 2nd commit
    2 files changed, 3 insertions(+), 1 deletion(-)
    create mode 100644 2nd.txt
    ```

    git 日志/恢复

    ```shell
    # 查看提交日志
    $ git log
    commit 1aba0bc1315ef4bac909fd6ab90a68e49df9aba8 (HEAD -> master)    # HEAD 表示当前版本指向
    Author: superman <superman@gmail.com>                               # 版本提交人
    Date:   Thu Feb 20 15:21:15 2020 +0800                              # 版本提交实际

        test git command:git diff/checkout/restore --- 2nd commit       # 版本提交备注

    commit bc42794d65e58cfa2cdc7637d8ce796002310275
    Author: superman <superman@gmail.com>
    Date:   Wed Feb 19 17:24:25 2020 +0800

        [feature] 1st commit
        [what] test local git test
        [why] nowhy
        [how]

    # 版本恢复，详细内容参考：https://git-scm.com/book/en/v2/Git-Tools-Reset-Demystified
    $ git reset --hard bc42794d65e58cfa2cdc7637d8ce796002310275
    HEAD is now at bc42794 [feature] 1st commit [what] test local git test [why] nowhy [how]
    # 查看仓库文件列表（目前已经恢复到第一次提交状态）
    $ ls -al
    total 9
    drwxr-xr-x 1 Administrator 197121  0  2月 20 16:41 ./
    drwxr-xr-x 1 Administrator 197121  0  2月 20 14:33 ../
    drwxr-xr-x 1 Administrator 197121  0  2月 20 16:41 .git/
    -rw-r--r-- 1 Administrator 197121 50  2月 20 16:41 test.txt
    $ cat test.txt
    create new file, file name:test.txt --- 1st commit

    # 自行搜索讲解，详细内容参考：https://git-scm.com/docs/git-reflog
    $ git reflog
    bc42794 (HEAD -> master) HEAD@{0}: reset: moving to bc42794d65e58cfa2cdc7637d8ce796002310275
    1aba0bc HEAD@{1}: commit: test git command:git diff/checkout/restore --- 2nd commit
    bc42794 (HEAD -> master) HEAD@{2}: commit (initial): [feature] 1st commit

    # 再次恢复到第二次提交
    $ git reset --hard 1aba0bc
    HEAD is now at 1aba0bc test git command:git diff/checkout/restore --- 2nd commit
    # 查看仓库文件列表（目前已经恢复到第二次提交状态）
    $ ls -al
    total 10
    drwxr-xr-x 1 Administrator 197121  0  2月 20 16:55 ./
    drwxr-xr-x 1 Administrator 197121  0  2月 20 14:33 ../
    drwxr-xr-x 1 Administrator 197121  0  2月 20 16:55 .git/
    -rw-r--r-- 1 Administrator 197121 49  2月 20 16:55 2nd.txt
    -rw-r--r-- 1 Administrator 197121 85  2月 20 16:55 test.txt
    $ cat test.txt
    create new file, file name:test.txt --- 1st commit
    test for git reset --- 2st commit
    $ cat 2nd.txt
    create new file, file name:2nd.txt --- 2st commit
    ```

4. 分支管理与多人协作

    分支合并没有冲突的情况：

    ```shell
    # 当前 git user 是 superman
    $ git config --global user.name
    superman
    # 从远程仓库更新本地仓库
    $ git pull
    Already up to date.
    # 创建切换一个分支，分支名:dev
    # 备注：新的 git 版本可以使用 "git switch -c" 创建切换分支
    $ git switch -c dev
    Switched to a new branch 'dev'
    # 修改 aaa 文件第二行，当前如下内容为：
    # “hello batman, I am superman, nice to meet you.”
    # 此时文件 aaa 中的内容为
    # “hello world 1st --- by batman”
    # “hello batman, I am superman, nice to meet you.”
    # 添加并提交
    $ git add aaa
    warning: LF will be replaced by CRLF in aaa.
    The file will have its original line endings in your working directory
    $ git commit aaa -m "no conflict case --- 1st commit"
    warning: LF will be replaced by CRLF in aaa.
    The file will have its original line endings in your working directory
    [dev 1a308d9] no conflict case --- 1st commit
    1 file changed, 1 insertion(+), 1 deletion(-)

    # 切换到主分支，新的 git 版本支持 "git switch" 命令
    $ git switch master
    Switched to branch 'master'
    Your branch is up to date with 'origin/master'.
    # 从远程仓库更新本地仓库
    $ git pull
    Already up to date.
    # 将 dev 分支变更的内容合并到主分支
    $ git merge dev
    Updating ebf69e2..1a308d9
    Fast-forward
    aaa | 2 +-
    1 file changed, 1 insertion(+), 1 deletion(-)
    # 查看仓库状态
    $ git status
    On branch master
    Your branch is ahead of 'origin/master' by 1 commit.
    (use "git push" to publish your local commits)
    nothing to commit, working tree clean
    # 将本地更新推送到远程仓库
    $ git push origin
    Enumerating objects: 5, done.
    Counting objects: 100% (5/5), done.
    Delta compression using up to 4 threads
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (3/3), 309 bytes | 309.00 KiB/s, done.
    Total 3 (delta 0), reused 0 (delta 0)
    To 106.13.211.199:/home/git/test.git
    ebf69e2..1a308d9  master -> master
    # 删除 dev 分支
    $ git branch -d dev
    Deleted branch dev (was 1a308d9).
    # 查看当前分支状态
    $ git branch
    * master
    ```

    ```shell
    # 当前 git user 是 batman
    $ git config --global user.name
    batman
    # 从远程仓库更新本地仓库（刚刚superman变更的内容已经更新到本地）
    $ git pull
    remote: Counting objects: 3, done.
    remote: Compressing objects: 100% (2/2), done.
    remote: Total 3 (delta 0), reused 0 (delta 0)
    Unpacking objects: 100% (3/3), done.
    From 106.13.211.199:/home/git
    ebf69e2..1a308d9  master     -> origin/master
    Updating ebf69e2..1a308d9
    Fast-forward
    aaa | 2 +-
    1 file changed, 1 insertion(+), 1 deletion(-)
    # 创建切换一个分支，分支名:dev
    # 备注：这是较旧的 git 版本，所以分支创建的命令是 "git checkout -b"
    $ git checkout -b dev
    Switched to a new branch 'dev'
    # 修改 aaa 文件第三行，添加如下内容为：
    # “hi, superman, nice to meet you too.”
    # 此时文件 aaa 中的内容为
    # “hello world 1st --- by batman”
    # “hello batman, I am superman, nice to meet you.”
    # “hi, superman, nice to meet you too.”
    # 添加并提交
    $ git add aaa
    $ git commit -m "no conflict case --- 2nd commit"
    [dev d09bd2d] no conflict case --- 2nd commit
    1 file changed, 1 insertion(+)

    # 切换到主分支
    $ git checkout master
    Switched to branch 'master'
    Your branch is up to date with 'origin/master'.
    # 从远程仓库更新本地仓库
    $ git pull
    Already up to date.
    # 将 dev 分支变更的内容合并到主分支
    $ git merge dev
    Updating 1a308d9..d09bd2d
    Fast-forward
    aaa | 1 +
    1 file changed, 1 insertion(+)
    # 查看仓库状态
    $ git status
    On branch master
    Your branch is ahead of 'origin/master' by 1 commit.
    (use "git push" to publish your local commits)
    nothing to commit, working tree clean
    # 将本地更新推送到远程仓库
    $ git push origin
    Counting objects: 3, done.
    Compressing objects: 100% (2/2), done.
    Writing objects: 100% (3/3), 314 bytes | 314.00 KiB/s, done.
    Total 3 (delta 0), reused 0 (delta 0)
    To 106.13.211.199:/home/git.git
    1a308d9..d09bd2d  master -> master
    # 删除 dev 分支
    $ git branch -d dev
    Deleted branch dev (was d09bd2d).
    # 查看当前分支状态
    $ git branch
    * master
    ```

    承接如上状态，查看出现冲突情况：

    ```shell
    # 当前远程仓库内容如下：
    $ cat aaa
    hello world 1st --- by batman
    hello batman, I am superman, nice to meet you.
    hi, superman, nice to meet you too.
    ```

    ```shell
    # 当前 git user 是 superman
    $ git config --global user.name
    superman
    $ cat aaa
    hello world 1st --- by batman
    hello batman, I am superman, nice to meet you.
    # 创建切换一个分支，分支名:dev
    $ git switch -c dev
    Switched to a new branch 'dev'
    # 修改 aaa 文件第三四行，添加如下内容为：
    # are you online ??? --- by superman
    # answer me... --- by superman
    # 此时文件 aaa 中的内容为
    # hello world 1st --- by batman
    # hello batman, I am superman, nice to meet you.
    # are you online ??? --- by superman
    # answer me... --- by superman

    # 添加并提交
    $ git commit -a -m "request batman's answer --- by superman"
    [dev 079da0e] request batman's answer --- by superman
    1 file changed, 2 insertions(+)
    # 切换到主分支
    $ git switch master
    Switched to branch 'master'
    Your branch is up to date with 'origin/master'.
    # 从远程仓库更新本地仓库
    $ git pull
    remote: Counting objects: 3, done.
    remote: Compressing objects: 100% (2/2), done.
    remote: Total 3 (delta 0), reused 0 (delta 0)
    Unpacking objects: 100% (3/3), 294 bytes | 1024 bytes/s, done.
    From 106.13.211.199:/home/git/test
    1a308d9..d09bd2d  master     -> origin/master
    Updating 1a308d9..d09bd2d
    Fast-forward
    aaa | 1 +
    1 file changed, 1 insertion(+)
    # 将 dev 分支变更的内容合并到主分支
    $ git merge dev
    Auto-merging aaa
    CONFLICT (content): Merge conflict in aaa
    Automatic merge failed; fix conflicts and then commit the result. # 自动合并失败，需要解决冲突
    # 查看冲突文件
    $ vi aaa
    hello world 1st --- by batman
    hello batman, I am superman, nice to meet you.
    <<<<<<< HEAD    # 当前最新版本内容
    hi, superman, nice to meet you too.
    =======         # 分割线
    are you online ??? --- by superman
    answer me... --- by superman
    >>>>>>> dev     # dev分支版本
    #整改后的分支内容
    hello world 1st --- by batman
    hello batman, I am superman, nice to meet you.
    hi, superman, nice to meet you too.
    are you online ??? --- by superman
    answer me... --- by superman
    # 添加并提交
    $ git commit -a -m "fix conlicts --- by superman"
    [master 0e0b607] fix conlicts --- by superman
    # 将本地更新推送到远程仓库
    $ git push origin
    Enumerating objects: 10, done.
    Counting objects: 100% (10/10), done.
    Delta compression using up to 4 threads
    Compressing objects: 100% (4/4), done.
    Writing objects: 100% (6/6), 599 bytes | 119.00 KiB/s, done.
    Total 6 (delta 1), reused 0 (delta 0)
    To 106.13.211.199:/home/git/test.git
    d09bd2d..0e0b607  master -> master
    # 删除 dev 分支
    $ git branch -d dev
    Deleted branch dev (was 079da0e).
    ```

5. 其他

    git 思维导图：[https://github.com/Kuri-su/GitMindMap-command](https://github.com/Kuri-su/GitMindMap-command)

    git 视频教程：[https://www.bilibili.com/video/av24441039/](https://www.bilibili.com/video/av24441039/)

    gitee：[https://gitee.com/](https://gitee.com/)
