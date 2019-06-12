---
title: 换电脑重新部署hexo
date: 2019-06-05 09:57:00
tags: 重新部署
categories: Hexo
---
<center>停更了一年又想写了，以前的本地文件早都不见了，又没有保存源文件...</center>
<!-- more -->

## 1. 在本地重新创建hexo
 - [创建方法](https://luke19950111.github.io/2018/03/03/Hexo-GitHub%E5%BB%BA%E7%AB%8B%E5%8D%9A%E5%AE%A2%E6%AD%A5%E9%AA%A4/)

## 2. 把md文件复制过来（所以要保存好源文件，在本项目仓库里新建一个分支或新开一个仓库存放）
 - 把以前写的所有md文件放到新创建的博客项目的`source/_posts`中

## 3. 重新部署 `hexo generate` `hexo deploy`

- 这样搞其实和新搭建了一个博客差不多，主题配置都没有了。
- 还好我存了md文件，要不然以前的博客也没了。
- 所以搭建好以后最好把源文件也上传到github上。