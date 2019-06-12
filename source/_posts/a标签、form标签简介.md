---
title: a标签、form标签简介
date: 2018-03-08 20:48:11
tags: [a, form]
categories: HTML5
---
<center>a标签和form标签</center>
<!-- more -->

## `<a>`元素<br>
`<a>`元素（锚元素），可以创建一个到其他网页、文件、同一页面内的位置或任何其他URL的超链接。

  一、`<a>`元素中target用法
    1.`<a href="http://qq.com" target="_blank">blank QQ</a>` 在空白页面中打开链接
    2.`<a href="http://qq.com" target="_self">self QQ</a>` 在当前页面打开链接
    3.`<a href="http://qq.com" target="_parent">parent QQ</a>` 在父级页面打开链接
    4.`<a href="http://qq.com" target="_top">top QQ</a>` 在顶层窗口打开链接

  二、`<a>`元素中download的用法
    1.`<a href="http://qq.com" download>下载</a>`
    2.若HTTP响应中Content-type为*Content-type：application/octet-stream*,同为下载。否则就要在`<a>`元素中写入download强制下载。
  
  三、`<a>`元素中href的用法
    1.无协议绝对地址
      `<a href="//qq.com">QQ</a>`不写协议，则自动继承当前协议，即浏览器会根据当前协议，补全无协议链接的协议。
    2.伪协议
      `<a href="javascript:alert(1);">QQ</a>`可以在用户点击链接时执行一段javascript代码
      用法：`<a href="javascript:;">QQ</a>`可以实现**点击之后没有任何动作的a元素**
      不可行写法：`<a href="#">QQ</a>` 页面会滚动到顶部，不可接受
                 `<a>QQ<a>`没有链接，`<a>`元素变成`<span>`元素的效果。`<a>`必须要有href
                 `<a href="">QQ</a>`会刷新页面，跳转自身页面，发送请求
    3.其他用法
      `<a href="xxx.html">QQ</a>`可以接一个路径，跳转到/xxx.html
      `<a href="#aabc">QQ</a>`锚点直接加到地址后面，不发起请求
      `<a href="?name=lk">QQ</a>`发起一个get请求
***
## `<form>`元素<br>
`<form>`元素表示文档中的一块区域，这块区域包含有交互控制元件，用来向web服务器提交信息。
  
  一、`<form>`元素是HTML提供的可以上传内容的方法，可用于发起POST请求
    1.`<form>`跳转页面与`<a>`的区别
      `<a>`跳转时发起HTTP get请求，`<form>`跳转时发起HTTP post请求
    2.
      ```
      <form action="users" method="post">
         <input type="text" name="username">
         <input type="password" name="password">
         <input type="submit" value="提交">
      </form>
      ```
      
      `<form>`action属性用来指定请求路径
      `<form>`默认也是发送HTTP get请求，method属性用来指定请求动词，所以要加参数`method="post"`
      HTTP请求有了第四部分：username="xxx"&password="222"。如果input不加name，那么在表单提交时，input的值就不会出现在请求里
      `<form>`必须要有提交按钮
  
  二、`<form>`元素也可以用来发送GET请求
    1.`<form action="users" method="get">` 不会将input的参数作为*HTTP请求的第四部分*，而是将参数写到*查询参数*里
      这样写没有意义，发送get请求直接用`<a>`就可以
    2.`<form action="users?xxx=1" method="post">`这样写可以让post也有查询参数
    3.以上，在`<form>`元素中
      get默认把参数放在查询参数里
      post默认把参数放在请求的第四部分
      可以让post也有查询参数
      不能让get有请求的第四部分
  
  三、`<form>`元素中target的用法
    `<form>`元素也有target，用法和`<a>`完全相同

     

  
