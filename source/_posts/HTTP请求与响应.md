---
title: HTTP请求与响应
date: 2018-04-16 21:56:47
tags: [HTTP, 请求, 响应]
categories: HTTP
---
<center>HTTP请求与响应，看做四个部分</center>
<!-- more -->

## 1.服务器与浏览器的交互
HTTP请求与响应通过Server+Client+HTTP实现：
- 客户端（ 浏览器）负责发起请求
- 服务器在80端口接受请求
- 服务器负责返回内容（响应）
- 浏览器负责下载响应内容

HTTP的作用就是指导浏览器和服务器如何进行沟通。
## 2. 请求的格式
可以把请求看作四个部分：
```
1 动词 路径 协议/版本
2 key1: value1
2 key2: value2
2 Host: baidu.com //希望访问
2 Accept: text/html //希望接受
2 Content-Type: application/x-www-form-urlencoded
3
4 要上传的数据
```
- 请求最多包含以上四个部分，最少包含三部分，第四部分可以为空；
- 第三部分永远都是一个回车（用于分开第二部分和第四部分）；
- 动词有GET（获取），POST（上传），PUT（整体更新），PATCH（局部更新），DELETE（删除），HEAD，OPTIONS等；
- 这里的路径包括**查询参数**，但不包括**锚点**；
- 如果没有写路径，那么路径默认为/；
- 第2部分中的Content-Type决定了第4部分的格式。
## 3. 响应的格式
响应同样看作四个部分：
```
1 协议/版本号 状态码 状态解释
2 key1: value1
2 key2: value2
2 Content-Type: text/html;charset=utf-8 //特殊，字符集为utf-8编码方式
2 Content-Length: 1000
3
4 要下载的内容
```
- 状态码：2xx 成功；3xx 重定向；4xx 客户端错误；5xx 服务器错误；
- 第2部分中的Content-Type 标注了第4部分的格式。