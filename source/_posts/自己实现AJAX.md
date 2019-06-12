---
title: 自己实现AJAX
date: 2018-05-03 16:49:01
tags: [AJAX, CORS, 请求, 响应, 解构赋值, Promise]
categories: HTTP
---
<center>阿甲克斯</center>
<!-- more -->

##### 如何发请求？
用form可以发请求，但是会刷新页面或新开页面；
用a可以发get请求，但是也会刷新页面或新开页面；
用image可以发get请求，但是只能以图片形式展示；
用link可以发get请求，但是只能以css、favicon的形式展示；
用script可以发get请求，但是只能以脚本的形式运行。

前端需要一种方式实现：
1. get、post、put、delete等请求都行
2. 想以什么方式展示就以什么方式展示

##### AJAX
AJAX(async javascript and XML) 异步的javascript和XML
满足如下技术叫AJAX：
1. 使用`XMLHttpRequest`发请求
2. 服务器返回XML格式的字符串（现已升级为返回JSON格式字符串）
3. JS解析XML，并更新局部页面

##### 原生JS发送AJAX请求
```
let request = new XMLHttpRequest()  //产生request
request.open('get','http://jack.com/xxx')  //配置request
request.send()  //发送request
request.onreadystatechange = () => {  //监听request状态的变化
    if(request.readyState === 4){  //请求响应都完毕了
        if(request.status >= 200 && request.status < 300){  //请求成功 3xx也有可能为成功，这里不考虑
            let string = request.responseText
            let object = window.JSON.parse(string)  //把符合JSON语法的字符串转换成JS对应的值
        }else if(request.status >= 400){ //请求失败
        }
    }
}
```

##### readyState
请求的5种状态

|值|状态|描述|
|:-:|:-:|:-:|
|0|UNSENT（未打开）|open()方法还没有调用|
|1|OPENED（未发送）|open()方法已经被调用|
|2|HEADERS.RECEIVED（已获取响应头）|send()方法已经被调用|
|3|LOADING（正在下载响应体）|响应体下载中；responseText中已经获取了部分数据|
|4|DONE（请求完成）|整个请求过程已经完毕|

```
setInterval( () => {
    console.log(request.readyState)
},1)  //试试每毫秒打印一次readyState
```
上面代码每毫秒打印一次`readyState`，会发现一般无法打印出所有的状态，因为请求的过程太快了。

##### onreadystatechange
使用`onreadystatechange`可以监听`readyState`的每次变化
```
request.onreadystatechange = () => {
    console.log(request.readyState)
}
```

##### status
该请求的响应状态码，只读。
```
if(request.status >= 200 && request.status < 300){ //3xx也可能成功，此处不考虑
    console.log('请求成功')
}else if(request.status >= 400){
    console.log('请求失败')
}
```

##### responseText
此次请求的响应文本。
当请求未成功或还未发送时为`null`。只读。

##### JS vs JSON
JSON和JS是两门不同的语言，道格拉斯（JSON作者）抄袭了JS，所以JSON很像JS。[JSON官网](https://www.json.org/)

- JSON没有function和undefined
- JSON的字符串首尾必须是双引号
- JSON不能表示复杂对象，只能表示哈希
- JSON没有变量
- JSON没有原型链

响应第四部分是字符串，可以是符合JSON语法的字符串。
```
//把符合JSON语法的字符串转换成JS对应的值
let string = request.responseText
let object = window.JSON.parse(string)
```
至此AJAX得到升级，不再返回XML格式的字符串，而是返回更亲近JS的JSON格式的字符串。

##### 同源策略
只有 **协议+端口+域名** 一模一样才允许发AJAX请求
因为AJAX可以读取响应内容，所以浏览器不允许一个域名的JS，在未经允许的情况下读取另一个域名的内容，但是浏览器并不阻止你向另一个域名发请求。
**CORS**可以告诉浏览器，我俩是一家的，别阻止他。
在jack.com的服务器写上
```
response.setHeader('Access-Control-Allow-Origin','http://frank.com')
```
这样frank.com就可以访问jack.com了。
**CORS** (Cross-Origin Resource Sharing) 跨来源资源共享。
突破同源策略 === 跨域

##### AJAX的所有功能
AJAX：用JS发送请求，用JS处理响应。

-  客户端JS发起请求（浏览器上的）
        1. 第一部分 `request.open('get','./xxx')` 动词，路径
        2. 第二部分 `request.setRequestHeader('Content-Type','x-www-form-urlencoded')`
        3. 第四部分 `request.send('a=1&b=2')`
- 服务端的JS发送响应（Node.js上的）
        1. 第一部分 `request.status/request.statusText` 例如`200/OK`
        2. 第二部分 `request.getResponseHeader()/request.getAllResponseHeaders()`
        3. 第四部分 `request.responseText`

通过AJAX可以任意设置请求四部分中的所有东西（有些不安全的不让设置），也可以获取响应四部分中的所有内容。

`XMLHttpRequest.setRequestHeader()`是设置HTTP请求头的方法，此方法必须在`open()`方法和`send()`方法之间调用。

##### 自己封装jQuery.ajax
```
window.jQuery.ajax = function(url, method, body, successFn, failFn){
    //... 
}
```
```
//给参数命名，有结构的参数--对象（JS里只有对象有结构）

window.jQuery.ajax = function(options){
    let url = options.url
    let method = options.method
    let body = options.body
    let successFn = options.successFn
    let failFn = options.failFn
    
    let request = new XMLHttpRequest()
    request.open(method, url) 
    /*for(let key in headers) {
      let value = headers[key]
      request.setRequestHeader(key, value)
    }*/ //设置headers，下文
    request.onreadystatechange = ()=>{
      if(request.readyState === 4){
        if(request.status >= 200 && request.status < 300){
          successFn.call(undefined, request.responseText)
        }else if(request.status >= 400){
          failFn.call(undefined, request)
        }
      }
    }
    request.send(body)
  }
//调用
let obj = {
    url: '/xxx',
    method: 'get',
    successFn: () => {}
    failFn: () => {}
}
window.jQuery.ajax(obj)
```
```
//调用可以直接写成
window.jQuery.ajax({
    url: '/xxx',
    method: 'get',
    successFn: () => {}
    failFn: () => {}
})
```
```
//如果请求成功时需要执行两个函数
successFn: (x) => {
    f1.call(undefined,x)
    f2.call(undefined,x)
}
```

**如果需要设置headers**
```
headers: {
    'Content-Type': 'application/x-www-form-urlencoded',
    'jack': '18'
}

//在open()和send()之间遍历headers，设置请求头
let headers = options.headers

for(let key in headers) {
      let value = headers[key]
      request.setRequestHeader(key, value)
}
```

**jQuery.ajax有两种传参方式，传一个参数，或传两个参数，第一个为url**

```
window.jQuery.ajax = function(options){
    let url
    if(arguments.length === 1){ //传一个参数
        url = options.url
    }else if(argument.length === 2){ //传两个参数
        url = arguments[0]  //url是第一个参数
        options = arguments[1]
    }
    let method = options.method
    let body = options.body
    let successFn = options.successFn
    let failFn = options.failFn
    let headers = options.headers
    //...
}
```

##### ES6语法之解构赋值
解构赋值语法是一个JavaScript表达式，这是的可以将 **值从数组** 或 **属性从对象** 提取到不同的变量。
```
    let url = options.url
    let method = options.method
    let body = options.body
    let successFn = options.successFn
    let failFn = options.failFn
    let headers = options.headers

//上面代码可以写成
let{url,mmethod,body,successFn,failFn,headers} = options

//直接从第一个参数里解构，拿到几个变量，同时用let声明几个变量
window.jQuery.ajax = function({url,method,body,successFn,failFn,headers}){}
```

##### Promise
不同的库风格不一样，如果不看文档，不知道成功时传什么参数，失败时传什么参数。
例如我自己封装的jQuery.ajax中，成功时传successFn，失败时传failFn，与原版的jQuery是不同的。
```
$.ajax({
    url:'/xxx',
method: 'get',
}).then(
    (responseText) => {console.log(responseText)}, //成功时执行第一个参数
    (request) => {console.log'error'} //失败时执行第二个参数
)
```
上面代码中，使用`.then()`，它有两个函数作为参数，成功时执行第一个参数，失败时执行第二个参数，不用再给成功、失败时执行的函数命名。

`.then()`后面可以继续续接`.then()`，可以基于上一次的处理结果继续处理。

##### 把自己封装的jQuery.ajax变成promise形式
```
window.jQuery.ajax = function({url, method, body, headers}){
    return new Promise(function(resolve, reject){
      let request = new XMLHttpRequest()
      request.open(method, url) // 配置request
      for(let key in headers) {
        let value = headers[key]
        request.setRequestHeader(key, value)
      }
      request.onreadystatechange = ()=>{
        if(request.readyState === 4){
          if(request.status >= 200 && request.status < 300){
            resolve.call(undefined, request.responseText) //成功调用resolve
          }else if(request.status >= 400){
            reject.call(undefined, request)  //失败调用reject
          }
        }
      }
      request.send(body)
    })
  } //返回值是一个Promise实例对象，这个实例对象有then属性；then也会返回一个Promise对象，所以可以再接.then()
  
```

[全部代码](https://github.com/Luke19950111/nodejs-test-AJAX)