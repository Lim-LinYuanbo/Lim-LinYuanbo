---
#layout: mypost
title: Hexo + Github Pages + Github Actions
categories:
- [运维]
tags:
- 运维
---

> 1.简单记录一下使用 Hexo 搭建博客，部署在 Github pages，通过 Github Actions 自动发布部署。

### Hexo 搭建与确认

参考：[https://hexo.io/zh-cn/docs](https://hexo.io/zh-cn/docs)

```shell
# 安装，初始化，启动
$ sudo npm install -g hexo-cli
$ mkdir blog
$ cd blog/
$ hexo init
$ hexo s
```

登录 `http://localhost:4000/` 确认正常显示表示搭建成功

### Github Pages 创建与部署

1. 仓库创建与提交

    参考：[Creating a GitHub Pages site](https://docs.github.com/en/pages/getting-started-with-github-pages/creating-a-github-pages-site)

    ```shell
    $ pwd
    /home/gittest/blog
    $ git init
    $ git add .
    $ git commit -m "first commit"
    $ git branch -M main
    # your_repo_url 仓库地址
    $ git remote add origin your_repo_url
    $ git push -u origin main
    ```

2. 部署与测试

    参考：[One-command deployment](https://hexo.io/docs/github-pages.html#One-command-deployment)

    ```shell
    # 安装插件
    $ npm install hexo-deployer-git --save

    # 仓库地址+部署分支
    $ vi _config.yml
    deploy:
      type: git
      repo: https://github.com/<username>/<project>
      branch: gh-pages

    $ hexo d
    ```

3. Github pages 配置与发布

    参考：[Configuring a publishing source for your GitHub Pages site](https://docs.github.com/en/pages/getting-started-with-github-pages/configuring-a-publishing-source-for-your-github-pages-site)

    此时在仓库分支处已经可以看见 `gh-pages` 了，说明刚刚执行 `deploy` 已经成功。

4. 确认与测试

### Github Actions 使用与编写

参考：[https://docs.github.com/en/actions](https://docs.github.com/en/actions)

1. 查找可用合适的方案

    参考：[Finding and customizing actions](https://docs.github.com/en/actions/learn-github-actions/finding-and-customizing-actions)

    进入[GitHub Marketplace page](https://github.com/marketplace)通过搜索 `hexo`，选择星星最多的项目，例如：[Hexo Action](https://github.com/marketplace/actions/hexo-action)

2. 依次执行

    > 1. Run the following terminal command, replacing the email with one connected to your GitHub  account.
    > 2. In Github Pages repo: Add the contents of the public key within your repositories deploy   keys menu. You can find this option by going to Settings > Deploy Keys, you can name the public   key whatever you want, but you do need to give it write access.
    > 3. In hexo source code repo: Add the contents of the private key to the Settings > Secrets    menu as DEPLOY_KEY.

    步骤一：生成密钥，由于前面已经部署成功，所以就使用那台机子的密钥就行。

    步骤二：由于本机已经可以访问 Github 仓库，所以可以忽略该步骤。

    步骤三：将可访问 Github 仓库的私钥放于 Github Pages 仓库 `Settings > Secrets`，并命名为     `DEPLOY_KEY`。

3. 编写 Actions

    使用 Actions 仓库模板，删除未使用的 `user_name` `user_email` `commit_msg` ，如下：

    ```yml
    name: Deploy

    on: [push]

    jobs:
      build:
        runs-on: ubuntu-latest
        name: A job to deploy blog.
        steps:
        - name: Checkout
          uses: actions/checkout@v1
          with:
            submodules: true # Checkout private submodules(themes or something else).

        # Caching dependencies to speed up workflows. (GitHub will remove any cache entries that    have not been accessed in over 7 days.)
        - name: Cache node modules
          uses: actions/cache@v1
          id: cache
          with:
            path: node_modules
            key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
            restore-keys: |
              ${{ runner.os }}-node-
        - name: Install Dependencies
          if: steps.cache.outputs.cache-hit != 'true'
          run: npm ci

        # Deploy hexo blog website.
        - name: Deploy
          id: deploy
          uses: sma11black/hexo-action@v1.0.3
          with:
            deploy_key: ${{ secrets.DEPLOY_KEY }}
        # Use the output from the `deploy` step(use for test action)
        - name: Get the output
          run: |
            echo "${{ steps.deploy.outputs.notify }}"
    ```

    保存，提交，查看运行结果。

4. 修改源码，提交，推送，查看结果

    ```shell
    $ git pull
    $ vi source/_posts/hello-world.md
    # 任意修改添加测试内容
    $ git add .
    $ git commit -m "111"
    $ git push
    ```

### 整体工作流程与思路

1. 本地修改源码，提交推送
2. Github 收到 push 请求，触发 Actions
3. Actions 动作大致为基于 `ubuntu-latest`（所以需要私钥），发布部署主页。
