---
title: 使用Travis CI 部署 Hexo 博客
categories: Hexo
date: 2019-09-26 09:22:57
tags:
description:
copyright:
---

Travis_CI是什么
[wiki](https://en.wikipedia.org/wiki/Travis_CI) Travis 是一项托管集成服务，用于构建和测试Github上托管的软件项目

什么是继续集成：
持续集成是经常合并小的代码更改的实践，而不是在开发周期结束时合并大的更改。目的是通过以较小的增量开发和测试来构建更健康的软件

解决了什么问题
1. 把发布博客的功能转交给了Travis，Travis读取github上面的源码，来进行构建。你要做的只能把file.md文件推送到github

本文档操作流程：
1. 把hexo github的源码托管在github
2. 在github上生成token
3. 在travis中使用github帐号登录
4. 在github源码中加入.travis.yml配置文件

## 1.把hexo github的源码托管在github
把代码托管在github上有两种方式：
1. 新建一个独立的仓库
2. 在你的username.github.io 仓库下，使用分支托管

本文使用方式二，进行操作。
1. 新建分支
~~~
git checkout --orphan hdoc
~~~

2. 删除该分支下的文件
~~~
git rm -rf .
~~~

3. 提交
~~~
git commit -am "message"
~~~

4. 在该分支下没有文件，在github网页上，不可见该分支
创建文件，并上传，查看是否分支创建成功
~~~
touch text.txt 创建文件
vim text.txt  打开文件 键入 “i” 输入测试文本内容 按“Esc” 输入“:wq” 保存退出
git add . 添加更新
git commit -m "message"
git push(如果该方式无法执行成功，使用如下命令：git push origin hdoc:hdoc（hodc为你刚刚创建的分支）;拉去代码的命令为：git pull origin hdoc:hdoc(hdoc为分支名)
~~~
以上命令以后，就可以在github页面上，看到你的分支

然后，在该分支下，把你的github源码粘贴到在分支下，并使用上面的命令提交代码：git add/commit/push
上传的github代码文件可如图：
![](https://img2018.cnblogs.com/blog/1273088/201909/1273088-20190926102046544-1241401079.png)

### 在github上生成token
[https://github.com/settings/tokens](https://github.com/settings/tokens)

按图操作即可
![](https://img2018.cnblogs.com/blog/1273088/201909/1273088-20190926095941334-38661650.png)

在保存完该页面以后，下一个页面就会出现token，在这里保存下来，会提示你：在以后不会再出现。切记！

### 在travis中使用github帐号登录
[https://travis-ci.org/](https://travis-ci.org/) 使用你的github帐号登录

然后：
![](https://img2018.cnblogs.com/blog/1273088/201909/1273088-20190926100821412-701824904.png)

继续 在输入name（该name在github的配置文件中需要使用），value为你刚才github token 然后add保存
![](https://img2018.cnblogs.com/blog/1273088/201909/1273088-20190926101012480-1078141381.png)

### 在github源码中加入.travis.yml配置文件
.travis.yml配置内容如下：
~~~
# 指定语言环境
language: node_js
# 指定需要sudo权限
sudo: required
# 指定node_js版本
node_js:
  - 10.16.3
# 指定缓存模块，可选。建议开启，
cache:
  directories:
    - node_modules

# 指定博客源码分支，因人而异。hexo博客源码托管在独立repo则不用设置此项
branches:
  only:
    - hdoc # 为你创建的分支名

before_install:
  - npm install -g hexo-cli

# Start: Build Lifecycle
install:
  - npm install
  - npm install hexo-deployer-git --save

# 执行清缓存，生成网页操作
script:
  - hexo clean
  - hexo generate

# 设置git提交名，邮箱
after_script:
  - git config user.name "username"
  - git config user.email "user email"
  # 替换同目录下的_config.yml文件中gh_token字符串为travis后台刚才配置的变量，注意此处sed命令用了双引号。单引号无效！
  # access_token为你在travis配中的变量名，自行更换，如果不一样，travis配置将不成功
  - sed -i "s/access_token/${ACCESS_TOKEN}/g" ./_config.yml
  - hexo deploy
# End: Build LifeCycle

~~~

修改_config.yml配置文件
~~~
修改前
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/SuperTrampAI/SuperTrampAI.github.io.git

修改后
# Deployment
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://access_token@github.com/SuperTrampAI/SuperTrampAI.github.io.git
~~~

然后新建md文件，推送到github。travis自动构建

参考：
[travis](https://juejin.im/post/5a1fa30c6fb9a045263b5d2a)
[travis](https://zhuanlan.zhihu.com/p/37014376)
[创建空白分支](https://segmentfault.com/a/1190000004931751)
[git 创建本地分支、提交到远程分支](https://blog.csdn.net/liuxiao723846/article/details/55191669)
