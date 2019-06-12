---
title: JSONP是什么
date: 2018-04-29 01:02:22
tags: [JSONP, SRJ, 数据库]
categories: HTTP
---
<center>JSONP, SRJ, 数据库</center>
<!-- more -->

### 前端操作数据库
首先，我们试着通过前端来操作一个数据库。
数据库是什么？只要能长久的存数据，就是数据库。文件系统是一种数据库。
首先我们来做一个简单的加法运算：
打开我们自己写的server
 [自己写server的方法](https://www.jianshu.com/p/c1f9769e965d)
[Node.js脚本](https://github.com/Luke19950111/JSONP-demo/blob/master/server.js)

```
if (path === '/') {  // 如果用户请求的是 / 路径
        var string = fs.readFileSync('./index.html')  // 就读取 index.html 的内容
        response.setHeader('Content-Type', 'text/html;charset=utf-8')  // 设置响应头 Content-Type
        response.end(string)   // 设置响应消息体
    }else if (path === '/style.css') {   // 如果用户请求的是 /style.css 路径
        var string = fs.readFileSync('./style.css')
        response.setHeader('Content-Type', 'text/css')
        response.end(string)
    }else if (path === '/main.js') {  // 如果用户请求的是 /main.js 路径
        var string = fs.readFileSync('./main.js')
        response.setHeader('Content-Type', 'application/javascript')
        response.end(string)
    }else {  // 如果上面都不是用户请求的路径
        response.statusCode = 404
        response.setHeader('Content-Type', 'text/html;charset=utf-8')  // 设置响应头 Content-Type
        response.end('找不到对应的路径，你需要自行修改 index.js')
    }
```
编辑index.html
```
<title>首页</title>
<link rel="stylesheet" href="/style.css">

<h5>您的账户余额是<span id="amount">100</span></h5>
<button id="button">付款1块钱</button>
<script>
    button.addEventListener('click',(e) => {
        amount.innerText = amount.innerText - 1
    })
</script>
```
访问server给出的地址得到如下页面
![页面1.JPG](https://upload-images.jianshu.io/upload_images/11014353-9c5398cd1e5a746d.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
每次点击按钮，余额减1。
但是这只是改变了页面上的显示，数据没有长久的保存下来，刷新页面，余额又变回100。为了长久地保存数据，我们需要使用数据库。

进入server所在的目录（我们所有的操作都在这个安全的目录里进行），`touch db`，创建一个文件db，内容为`100`，这个db就是我们的数据库，里面存着我们的余额。

将HTML中固定的余额100换成一个占位符，我这里用`&&&amount&&&`代替：
```
<h5>您的账户余额是<span id="amount">&&&amount&&&</span></h5>
```
让server可以读取到数据库，并将数据放到页面中
```
var amount = fs.readFileSync('./db', 'utf8')
string = string.replace('&&&amount&&&',amount)
```
现在，当我们点击按钮时，不能再自己改页面上的余额，而是应该告诉服务器去改变数据库，然后再刷新页面，让服务器告诉我们最新的结果。

这就需要向服务器发起POST请求。

我们用form发起请求，请求路径到/pay
```
<title>首页</title>
<link rel="stylesheet" href="/style.css">

<h5>您的账户余额是<span id="amount">&&&amount&&&</span></h5>

<form action="/pay" method="post">
    <input type="submit" value="付款1块钱">
</form>
```
让server对`/pay`做出响应：
```
else if (path === '/pay' && method.toUpperCase() === 'POST'){
        var amount = fs.readFileSync('./db','utf8')
        var newAmount = amount - 1
        fs.writeFileSync('./db',newAmount)
        response.end('success')
    } //从数据库拿到数据，减一，再存到数据库，返回success
```
重启服务器，刷新页面。（每次修改server.js都要进行这一步）
现在，我们每次点击按钮，都会在/pay页面返回一个success，返回上一个页面再刷新，可以看到余额少了1块。这次便是数据库中的余额少了1，我们实现了对数据库的操作。

### SRJ方案
我们已经实现了对数据库的操作，但是用户体验并不好，每次付款后还要再返回上一页面并刷新才能看到余额，而且form每次提交时都刷新当前页面。所以我们需要优化用户体验。
1. 我们可以用一个iframe来避免刷新当前页面，在iframe里刷新：
```
<title>首页</title>
<link rel="stylesheet" href="/style.css">

<h5>您的账户余额是<span id="amount">&&&amount&&&</span></h5>

<form action="/pay" method="post" target="result">
    <input type="submit" value="付款1块钱">
</form>
<iframe name="result" src="about:blank" frameborder="0" height="200"></iframe>
```
2. 我们可以通过动态创建一个script发请求：
```
<title>首页</title>
<link rel="stylesheet" href="/style.css">

<h5>您的账户余额是<span id="amount">&&&amount&&&</span></h5>
<button id="button">打1块钱</button>
<script>
    button.addEventListener('click',function(){
        let script = document.createElement('script')
        script.src = '/pay'
        document.body.appendChild(script)
        amount.innerText = amount.innerText - 1 //局部刷新页面，优化体验
        script.onload = function(e){
            e.currentTarget.remove()
        } //每次点击按钮页面中都会出现一个script，移除它
    })
</script>
```
这样每次点击按钮的时候会动态创建一个script，src指向响应方（/pay）；
由于页面出现指向/pay的script，浏览器先执行/pay里的响应内容，/pay里可以直接`alert("success")`，所以不需要在html里再写`script.onload = function(){alert('success')}`（但还是要监听它，移除每次点击在页面里产生的script）。
动态创建script的时候只能发送GET请求，不能发送POST请求。
修改server.js：
```
else if (path === '/pay'){
        var amount = fs.readFileSync('./db','utf8')
        var newAmount = amount - 1
        fs.writeFileSync('./db',newAmount)
        response.setHeader('Content-Type', 'application/javascript')
        response.end('alert("success")')
    }
```
现在每次点击按钮都会弹出success，并且余额减1。
也可以在服务器里修改页面中的余额：
```
else if (path === '/pay'){
        var amount = fs.readFileSync('./db','utf8')
        var newAmount = amount - 1
        fs.writeFileSync('./db',newAmount)
        response.setHeader('Content-Type', 'application/javascript')
        response.end('amount.innerText = amount.innerText - 1') //不再弹出success，直接让余额减1。作为javascript执行，一是上面Content-Type规定，另外是由一个script标签引到这里来的。}
```
现在每次点击按钮，余额减1，页面没有刷新。用户感觉不到这是在操作远程，用户体验已经得到了很好的提升。

这种服务器返回JavaScript的方案就叫做**SRJ方案（server rendered javascript）**,是Ajax出现之前的无刷新局部更新页面内容方案。

##### 请求另一个网站的script
修改hosts：
127.0.0.1 frank.com
127.0.0.1 jack.com 
现在在本地访问frank.com和jack.com都是访问127.0.0.1
开两个server，分别给端口号8001，8002
`PORT=8001 node server.js`
`PORT=8002 node server.js`
分别访问 frank.com:8001 和 jack.com:8002(这是两个网站，只不过它们的源代码是一样的)
我们可以让 frank.com的前端去访问jack.com的后端：
```
script.src = 'http://jack.com:8002/pay'
```

### JSONP
上面我们用frank.com的前端去访问jack.com的后端，依然可以使页面上的余额发生改变。这是因为jack.com的后端有这样的代码：
```
response.end('amount.innerText = amount.innerText - 1')
```
这就要求jack.com的后端程序员对frank.com的页面细节了解很清楚，前后端代码无法分离。（耦合）
我们不需要让jack.com的后端程序员了解frank.com的页面，只需要让他调用由frank.com前端传给他的一个函数，作为响应，函数由frank.com的前端写在页面里。
frank.com的前端：
```
window.xxx = function(result){
    alert('得到的结果是${result}')
}
```
jack.com的后端：
```
response.end(
    xxx.call(undefined, 'success')
) //调用frank.com前端给的函数xxx，并传参success
```
这样，jack.com的后端程序员不需要直到任何页面细节，只需调用函数xxx，成功给参数success，失败给参数fail。 （解耦）

现在不能让jack.com的后端和frank.com的前端有任何耦合，把函数名xxx作为参数传过去：
```
script.src = 'http://jack.com:8002/pay?callbackName=xxx'
```

```
response.end(
    `${query.callbackName}.call(undefined, 'success')` //这就是JSONP JSON(这里是个字符串success，特殊情况这个字符串由JSON替代)+padding
)
```
jack.com的后端调用函数，传了一个参数’success‘。特殊情况这个参数是JSON，这种方案就叫JSONP。（动态标签跨域请求）

### 文字叙述JSONP
请求方：一个网站(frank.com)的前端 （浏览器）
响应方：另一个网站(jack.com)的后端 （服务器）
1. 请求方创建script，src指向响应方。同时传一个查询参数`?callbackName=xxx`
2. 响应方根据查询参数callbackName，构造形如`xxx.call(undefined, '你要的数据')`或`xxx('你要的数据')`这样的响应
3. 浏览器收到响应，就会执行`xxx.call(undefined, '你要的数据')`
4. 那么请求方就直到了他要的数据

这就是JSONP。

##### 约定
1. callbackName 用 callback
2. xxx（函数名）用随机数（不会污染全局变量）

```
button.addEventListener('click',function(){
        let script = document.createElement('script')
        let functionName = 'jack' + parseInt(Math.random() * 100000000, 10) //取随机数
        window[functionName] = function(result){
            if(result === 'success'){
            amount.innerText = amount.innerText - 1
            }
        }
        script.src = 'http://jack.com:8002/pay?callback=' + functionName //随机数作函数名
        document.body.appendChild(script)
        script.onload = function(e){
            e.currentTarget.remove()
            delete window[functionName] //随机函数名用完就删掉
        }
        script.onerror = function(e){
            alert('fali')
            e.currentTarget.remove()
            delete window[functionName] //随机函数名用完就删掉
        }
 })
```

##### jQuery API
```
  $.ajax({
        url: "http://jack.com:8002/pay",
        dataType: "jsonp",
        success: function(response){
            if(response === 'success'){
                amount.innerText = amount.innerText - 1
            }
        }
    })
```