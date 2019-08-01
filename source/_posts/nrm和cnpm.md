---
title: nrm和cnpm
date: 2019-08-01 17:30:47
tags: [npm, nrm, cnpm]
categories: Node.js
---
<center>提高一下node模块下载速度吧</center>
<!-- more -->

# nrm -- [NPM registry manager](https://github.com/Pana/nrm)
- fast switch between different registries: npm, cnpm, nj, taobao...
- 安装 `npm install -g nrm`
- 罗列所有仓库镜像`nrm ls`
- 测试所有仓库镜像速度 `nrm test`
- 选择速度最快的镜像，例如，切换到taobao镜像： `nrm use taobao`
- 之后继续使用npm命令，即已经在使用新的镜像

# cnpm -- [淘宝NPM镜像](http://npm.taobao.org/)
- 默认npm镜像仓储是在国外，速度慢
- 淘宝npm镜像每隔10分钟将其更新到国内，速度快
- 安装 `npm install -g cnpm --registry=https://registry.npm.taobao.org`
- 使用cnpm命令 `cnpm install [name]`