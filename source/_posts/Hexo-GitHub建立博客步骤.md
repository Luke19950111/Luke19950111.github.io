---
title: Hexo+GitHub建立博客步骤
date: 2018-03-03 16:58:41
tags: hexo
categories: Hexo
---
<center>搭建此博客</center>
<!-- more -->

## 以下是步骤
  1.进入一个安全的目录，比如 cd~/Desktop
  2.在GitHub上新建一个空repo，repo名称为【你的用户名.github.io】，注意用户名一定要用真正的GitHub用户名，否则会遇到问题，亲测
  3.npm install -g hexo-cli，安装Hexo
  4.hexo init myBlog
  5.cd myBlog
  6.npm i
  7.hexo new 开博大吉，出现一个md文件路径
  8.start xxx.md，编辑这个md文件，Windows的路径中\需要变成/
  9.start _config.yml，编辑网站配置
    -第6行title改成博客名字
    -第9行author改成作者名字
    -最后一行 type 后加 git，注意冒号后空格
    -最后一行后新增一行，左边与 type 平齐，加上一行 repo：仓库地址，注意冒号后空格
  10.npm install hexo-deployer-git --save,安装git部署插件
  11.hexo deploy
  12.进入【你的用户名.github.io】对应的repo，打开GitHub Pages功能，点击预览链接
  13.你现在应该看到了你的博客！
## 更换主题
  1.https://github.com/hexojs/hexo/wiki/Themes 上面有主题合集
  2.选择主题，进入主题的GitHub首页，复制SSH或HTTPS地址，假设地址为git@github.com:iissnan/hexo-theme-next
  3.cd themes
  4.git clone  git@github.com:iissnan/hexo-theme-next.git
  5.cd ..
  6.将_config.yml的第75行改为主题名
  7.hexo generate
  8.hexo deploy
  9.刷新博客页面，看到新的主题
## 上传源代码
  1.【你的用户名.github.io】中只保存了博客，并没有【生成博客的程序代码】，需要再创建一个名为blog-generator的空仓库用来保存myBlog里的【生成博客的程序代码】。
  2.这样博客发布在【你的用户名.github.io】，而【生成博客的程序代码】发布在blog-generator，避免误删myBlog目录造成的损失。每次hexo deploy之后博客更新，再add/commit/push一下【生成博客的程序代码】。
