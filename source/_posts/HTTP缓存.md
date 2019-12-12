---
title: HTTP缓存
date: 2018-06-02 22:10:54
tags: [Cache-Control, Expires, Etag]
categories: HTTP
---
<center>Cache-Control, Expires, Etag</center>
<!-- more -->

***

## 缓存控制Cache-Control
Cache-Control是Web性能优化的一种，能加速HTTP请求与响应。

服务器中设置响应头：
```javascript
response.setHeader('Cache-Control', 'max-age=30')
```
30秒内不会再次请求浏览器从内存中把请求的内容拿给你。

例如，向服务器请求一个很大的main.js文件，下载这个文件需要较长时间，服务器在返回文件的同时在响应头中设置`Cache-Control: max-age=30`。30秒内再次请求同样的URL，浏览器不会再次发起请求，而是直接从内存中返回上次的main.js文件，这样就节约了再次下载文件的时间。30秒后再次请求这个main.js，服务器返回文件的同时再次设置Cache-Control，如此重复。

[Cache-Control MDN](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Cache-Control)

#### 如何更新缓存
一般设置Cache-Control的时间都会很久，比如一年，十年，尽量不再发请求。那么会有一个问题：如果页面有更新，用户没有办法获取最新的版本。

所以入口处（首页，html）不能设置Cache-Control。
如果全都设置了Cache-Control，用户每次访问都直接从内存中读取内容，不会向服务器发起任何请求，获取不到更新。
而留着入口不用缓存，每次请求首页都向服务器发起请求，如果其他文件有更新，就在入口处把URL变一下（不改变请求路径，例如加一个查询参数），这样浏览器就不会使用缓存，而是下载最新的版本，再把新版本缓存下来。

改变URL，例如，加一个查询参数：
```html
<link rel="stylesheet" href="./css/default.css">
//更新，变成
<link rel="stylesheet" href="./css/default.css/?v=2">
//再更新
<link rel="stylesheet" href="./css/default.css/?v=3">
```
上面代码还是在访问`/css`，但是URL不是以前的URL了，所以浏览器不会使用缓存，而是下载新的css。

## Expires
Expires头制定了一个日期/时间，在这个日期/时间之后，HTTP响应被认为是过时的，不再用缓存，重新请求。

如果还设置了"max-age"或"s-max-age"指令的`Cache-Control`响应头，那么`Expires`头就会被忽略。

`Cache-Control`是设置**多长时间后**过期，是一个时间长度；
`Expires`是设置**什么时候**过期，是一个时间点。

但是Expires设置的时间点指的是本地时间，如果用户本地时间错乱就会出问题。

## ETag

首先了解一下**MD5**。
MD5是一种摘要算法，用于确保信息传输完整一致。
简单来说，MD5把信息变成一个字符串，信息传输后再次用MD5解析，任何一点变化都会生成一个不同的字符串，且内容差异越小，生成的结果差异越大，把微小的差异放大。
[MD5](https://zh.wikipedia.org/wiki/MD5)

ETag HTTP响应头是资源特定版本标识符。
把文件的MD5放到ETag里设置响应头：
```javascript
let fileMd5 = md5(string)
response.setHeader('ETag', fileMd5)
```
在请求这个文件时，响应头里有了`ETag: Md5的值`，
再次请求同一个文件时，请求头里带着`If-None-Match: Md5值`，
也就是再次请求时带着一个版本号（Md5的值），如果版本号与上次相同，说明文件没有更新，不需要重新下载。
```javascript
let fileMd5 = md5(string)
response.setHeader('ETag', fileMd5)
if(request.headers['If-None-Match'] === fileMd5){
    //没有响应体
    response.statusCode = 304 //Not Modefied
}else{
    //有响应体
    response.write(string)
}
```

[ETag MDN](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/ETag)

用Cache-Control直接不发请求，
用ETag，要发请求，如果MD5一样，不下载。