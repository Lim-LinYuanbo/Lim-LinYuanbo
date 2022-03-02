---
#layout: mypost
title: Hexo Action
categories:
- [运维]
tags:
- 运维
---

> 简单理解学习一下 Hexo Action 这个仓库工作流程，顺便复习一下 Github Action。

### 脚本概览与相关注释

参考：

1. [Hexo Action](https://github.com/marketplace/actions/hexo-action)
2. [Workflow syntax for GitHub Actions](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)

```yml
name: Deploy

on: [push]

jobs:
  build:
    runs-on: ubuntu-latest
    name: A job to deploy blog.
    steps:
    - name: Checkout
      uses: actions/checkout@v1 # 使用 `https://github.com/marketplace/actions/checkout` 克隆该脚本仓库，使用 V1 版本，参考：jobs.<job_id>.uses
      with: # actions/checkout 输入参数
        submodules: true # Checkout private submodules(themes or something else).
    
    # Caching dependencies to speed up workflows. (GitHub will remove any cache entries that have not been accessed in over 7 days.)
    - name: Cache node modules
      uses: actions/cache@v1 # 这里使用的是 `https://github.com/marketplace/actions/cache`，目前未搞懂 cache 的作用。
      id: cache
      with: # 这里使用的例子是 `https://github.com/actions/cache/blob/main/examples.md#node---npm` 相关参数。
        path: node_modules
        key: ${{ runner.os }}-node-${{ hashFiles('**/package-lock.json') }}
        restore-keys: |
          ${{ runner.os }}-node-

    # 安装依赖
    - name: Install Dependencies
      if: steps.cache.outputs.cache-hit != 'true'
      run: npm ci
    
    # Deploy hexo blog website.
    - name: Deploy
      id: deploy
      uses: sma11black/hexo-action@v1.0.3 # 核心业务逻辑，跟进该作者仓库了解流程
      with:
        deploy_key: ${{ secrets.DEPLOY_KEY }}
        user_name: your github username  # (or delete this input setting to use bot account)
        user_email: your github useremail  # (or delete this input setting to use bot account)
        commit_msg: ${{ github.event.head_commit.message }}  # (or delete this input setting to use hexo default settings)
    # Use the output from the `deploy` step(use for test action)
    - name: Get the output
      run: |
        echo "${{ steps.deploy.outputs.notify }}"
```

### Hexo Action 的 `actions.yml`

参考：[https://github.com/sma11black/hexo-action/blob/master/action.yml](https://github.com/sma11black/hexo-action/blob/master/action.yml)

```yml
name: 'Hexo Action'
description: 'Hexo CI/CD action for automating deployment.'
author: 'sma11black'
inputs: # 输入参数
  user_name:
    description: 'Github user name'
    required: true
    default: 'github-actions[bot]' # 未设置的默认参数
  user_email:
    description: 'Github email'
    required: true
    default: '41898282+github-actions[bot]@users.noreply.github.com'
  deploy_key: # 根据该仓库说明，这个是必须设置的，为可访问部署仓库的私钥
    description: 'The deploy key for Github Pages repo'
    required: true
    default: ''
  commit_msg:
    description: 'Git commit messages to your GitHub Pages repository.'
    required: false
    default: ''
outputs:
  notify: # id of output
    description: 'The notification of deployment result'
runs: # 最终运行执行命令与设置
  using: 'docker' # 基于 docker 运行
  image: 'Dockerfile' # dockerfile 运行环境的配置与设置
  args: # 输入参数
    - ${{ inputs.user_name }}
    - ${{ inputs.user_email }}
    - ${{ inputs.deploy_key }}
    - ${{ inputs.commit_msg }}
branding:
  icon: 'package'  
  color: 'blue'
```

### Hexo Action 的 `Dockerfile`

参考：[https://github.com/sma11black/hexo-action/blob/master/Dockerfile](https://github.com/sma11black/hexo-action/blob/master/Dockerfile)

```dockerfile
# docker 基于的镜像
FROM node:12-buster-slim

LABEL version="1.0.3"
LABEL repository="https://github.com/sma11black/hexo-action"
LABEL homepage="https://sma11black.github.io"
LABEL maintainer="sma11black <smallblack@outlook.com>"

# 拷贝 宿主路径->容器路径
COPY entrypoint.sh /entrypoint.sh
COPY sync_deploy_history.js /sync_deploy_history.js

# docker 容器启动时执行的命令
RUN apt-get update > /dev/null && \
    apt-get install -y git openssh-client > /dev/null ; \
    chmod +x /entrypoint.sh

# 没完全理解该命令 :( ，但是一定执行了 `entrypoint.sh` 这个脚本 :)
ENTRYPOINT ["/entrypoint.sh"]
```

### Hexo Action 的 `entrypoint.sh`

参考：[https://github.com/sma11black/hexo-action/blob/master/entrypoint.sh](https://github.com/sma11black/hexo-action/blob/master/entrypoint.sh)

```shell
#!/bin/sh

set -e

# 从这里可以清晰的看出，使用私钥的作用，其他都是常规操作
# setup ssh-private-key
mkdir -p /root/.ssh/
echo "$INPUT_DEPLOY_KEY" > /root/.ssh/id_rsa
chmod 600 /root/.ssh/id_rsa
ssh-keyscan -t rsa github.com >> /root/.ssh/known_hosts

# setup deploy git account
git config --global user.name "$INPUT_USER_NAME"
git config --global user.email "$INPUT_USER_EMAIL"

# install hexo env
npm install hexo-cli -g
npm install hexo-deployer-git --save

# deployment
if [ "$INPUT_COMMIT_MSG" = "none" ]
then
    hexo g -d
elif [ "$INPUT_COMMIT_MSG" = "" ] || [ "$INPUT_COMMIT_MSG" = "default" ]
then
    # pull original publish repo
    NODE_PATH=$NODE_PATH:$(pwd)/node_modules node /sync_deploy_history.js
    hexo g -d
else
    NODE_PATH=$NODE_PATH:$(pwd)/node_modules node /sync_deploy_history.js
    hexo g -d -m "$INPUT_COMMIT_MSG"
fi

echo ::set-output name=notify::"Deploy complate."
```

### 整体思路

1. 设置 Github Action 触发条件`push on`；
2. Github Action 运行环境 ubuntu-latest，并设置相应环境;
3. 执行 `https://github.com/sma11black/hexo-action/blob/master/action.yml` 完成部署。
