---
title: Session
date: 2018-05-30 22:07:59
tags: Session
categories: HTTP
---
<center>Session这个东西还是要了解一下的</center>
<!-- more -->

在文章[Cookie与登录注册](https://www.jianshu.com/p/e8eade3815d3)中，我们已经实现了简单的注册和登录功能，并且登录后服务器会给客户端发送一个Cookie，登录后跳转首页可以显示自己的密码。

但是有一个问题：现在的Cookie是明文的，用户可以随意篡改Cookie，继而看到别人的密码。
```
if (found) {
    response.setHeader('Set-Cookie', `sign_in_email=${email}`)
    response.statusCode = 200
}
```
上面代码中，如果认证用户登录成功，在Set-Cookie时会直接把email暴露给用户。

Session可以解决这个问题。
首先在服务器中声明一个空对象sessions（sessions是服务器的一块内存）：（后端）
```
var sessions  = {}
```
当用户登录成功后，把一个随机数sessionId通过Cookie传到客户端，并把这个sessionId和其对应的用户email存入session中。这样，用户不能直接从Cookie中看到email，但服务器仍然可以根据Cookie中的sessionId得到相匹配用户信息。（后端）
```
if(found){
  let sessionId = Math.random() * 1000000
  session[sessionId] = {sign_in_email: email}
  response.setHeader('Set-Cookie', `sessionId = ${sessionId}`)
  response.statusCode = 200 
}
```
当用户跳转到首页时，根据Cookie从sessions中找到匹配的email：（后端）
```
let cookies = '' //cookie初始为空字符串，避免没有cookie时下面代码中cookies.length报错
if(request.headers.cookie){ //判断是否有cookie，避免没有cookie时split方法报错
    cookies = request.headers.cookie.split('; ')  // ['email=1@', 'a=1', 'b=2']
}

let hash = {}
for (let i = 0; i < cookies.length; i++) {
    let parts = cookies[i].split('=')
    let key = parts[0]
    let value = parts[1]
    hash[key] = value
}
let email = sessions[hash.sessionId].sign_in_email
```
注意，现在用的是我们自己写的node.js服务器，如果关闭服务器（每次改完服务器代码都要重启服务器），所有内存会释放，sessions就清空了（sessions是服务器中的一块内存）。一般服务器是不关闭的，如果要关闭，也会先把sessions存在硬盘中。

Session与Cookie的关系：
一般来说，Session基于Cookie来实现。

Cookie:
1. 服务器通过响应头Set-Cookie给客户端一串字符串
2. 客户端每次访问相同域名的网页时，请求头必须带上这段字符串
3. 客户端要在一段时间内保存这个Cookie
4. Cookie默认在用户关闭页面后就失效，后台代码可以任意设置Cookie的过期时间
5. 大小大概在4kb以内

Session：
1. 将SessionID（随机数）通过Cookie发给客户端
2. 客户端访问服务器时，服务器读取SessionID
3. 服务器有一块内存（哈希表）保存了所有的session
4. 通过SessionID服务器可以得到对应用户的隐私信息，如id，Email
5. 这块内存（哈希表）就是服务器上的所有session

#### Session可以用LocalStorage+查询参数实现
一般来说，Session是基于Cookie实现的，因为Session将SessionID放在Cookie里发送给客户端。
但是，也可以不使用Cookie，用LocalStorage+查询参数来实现Session。

当认证用户登录成功后，不再Set-Cookies，而是直接把SessionID通过JSON传给前端：（后端）
```
if(found){
  let sessionId = Math.random() * 1000000
  sessions[sessionId] = {sign_in_email: email}
  response.write(`{"sessionId": ${sessionId}}`)
  response.statusCode = 200
}
```

前端拿到SessionID后把它存到LocalStorage里备用（因为客户端要在一段时间内持有这个SessionID），然后把SessionID作为查询参数插入到要跳转的页面：（前端 sign_in.html）
```
$post('/sign_in', hash)
    .then{(response) => {
            let object = JSON.parse(response)
            localStorage.setItem('sessionId', object.sessionId)
            window.local.href = `/?sessionId=${object.sessionId}`
        }, (request) =>{
            alert('邮箱与密码不匹配')
        }}
```

再向首页发起请求时，服务器从查询参数中拿到SessionID，并在sessions中找到匹配的用户信息：（后端）
```
let mySession = sessions[query.sessionId]
let email
if(mySession){
    email = mySession.sign_in_email
}
```

完