---
title: Blog-Gitalk
categories: default
date: 2019-07-21 10:47:32
tags:
description:
copyright:
---

为自己的博客网站集成Gitalk评论插件

<!-- more -->

在[本文]((https://supertrampai.com/2019/06/01/%E5%85%A5%E5%9D%91%E7%88%AC%E8%99%AB/#more))末尾可查看先查看效果：

## 1. 新建New OAuth App
在github中，Settings  /  Develpoer settings OAuth Apps  /  New OAuth App
![i](https://upload-images.jianshu.io/upload_images/4319370-52969fe1eb3f8d33.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

> bolg-comments
> https://github.com/SuperTrampAI
> bolg-comments gitalk
> https://supertrampai.com

分别在对应的栏目内填入相对应的内容

注意：在Authorization callback URL 中，不应该加上“/”

## 2. 创建github仓库，用来放置评论的文本

在你的github中，新建**repository**，命名如下：**blog-comments** （可替换）

---

下图为bolg架构
![i.png](https://upload-images.jianshu.io/upload_images/4319370-2d8126af3b3979f1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

总共需要操作的几个文件，加一个**_config.yml**文件

---

## 3. 新建gitalk.swig
文件目录：themes\hexo-theme-next-master\layout\_third-party\comments
文件内容如下：
```
{% if [page.comments](http://page.comments/) && theme.gitalk.enable %}

<link rel="stylesheet" href="[https://unpkg.com/gitalk/dist/gitalk.css](https://unpkg.com/gitalk/dist/gitalk.css)">

<script src="[https://unpkg.com/gitalk/dist/gitalk.min.js](https://unpkg.com/gitalk/dist/gitalk.min.js)"></script>

   <script type="text/javascript">

        var gitalk = new Gitalk({

          clientID: '{{ theme.gitalk.clientID }}',

          clientSecret: '{{ theme.gitalk.clientSecret }}',

          repo: '{{ theme.gitalk.repo }}',

          owner: '{{ theme.gitalk.githubID }}',

          admin: ['{{ theme.gitalk.adminUser }}'],

          id: location.pathname,

          distractionFreeMode: '{{ theme.gitalk.distractionFreeMode }}'

        })

        gitalk.render('gitalk-container')

       </script>

{% endif %}
```

## 4. 修改index.swig
在gitalk.swig的同级目录下找到index.swig

在文件末尾加上：

```
{% include 'gitalk.swig' %}
```
 
如果你的gitalk.swig文件不是采取的如上命名，自行替换

## 5. 修改comments.swig
目录：themes\hexo-theme-next-master\layout\_partials\comments.swig

在如下目录的最后一个if代码块内加入如下代码：
```
{% elseif theme.gitalk.enable %}
<div id="gitalk-container"></div>
```

**此处划重点！！！！**代码块必须放入，如图片所示位置！
![i.png](https://upload-images.jianshu.io/upload_images/4319370-d6a70bc0d0ff782c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 6. 修改_config.yml

在themes\hexo-theme-next-master\_config.yml如下位置加入代码块：
```
gitalk:

  enable: true

  githubID: SuperTrampAI  # 你的githubId

  repo: blog-comments # 新建的repository名

  clientID: # 申请的OAuth AppID

  clientSecret:  # 申请的OAuth AppID

  adminUser: SuperTrampAI # 

  distractionFreeMode: true
```

到此结束，可以在[本文](https://supertrampai.com/2019/06/01/%E5%85%A5%E5%9D%91%E7%88%AC%E8%99%AB/#more)最后看到效果
