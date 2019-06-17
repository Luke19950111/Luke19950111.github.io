---
title: vue实例事件
date: 2019-06-17 14:47:25
tags: [vue, 实例事件, $on, $once, $off, $emit]
categories: vue
---
<center>$on, $once, $off, $emit</center>
<!-- more -->

vue有实例属性，实例方法，实例事件。本文讲实例事件。

# $on (在构造器外部添加事件)

`$on`接收两个参数，第一个参数是调用时的事件名称，第二个参数是一个匿名方法

```
app.$on('reduce', function(){
    console.log('执行了reduce()')
    this.count--
})
```

# $once (执行一次的事件)

```
app.$once('reduceOnce', function(){
    console.log('只执行一次')
    this.count--
})
```

# $off (关闭事件)

```
function off(){
    console.log('关闭事件')
    app.$off('reduce')
}
```

# $emit (事件调用)

```
function reduce(){
    console.log('事件调用')
    app.$emit('reduce')
}
```

# 完整示例代码

用`http-server`访问，看控制台输出，帮助理解

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Vue入门之Helloworld</title>
    <script src="https://cdn.jsdelivr.net/npm/vue/dist/vue.js"></script>
</head>
<body>
    <div id="app">
        <div>数字：{{count}}</div>
        <button onclick="reduce()">on调用</button>
        <button onclick="reduceOnce()">once调用</button>
        <button onclick="off()">off调用</button>
    </div>

<script type="text/javascript">
var app = new Vue({
    el:'#app',
    data:{
        count: 1
    }
})
// $on 在构造器外部添加事件
app.$on('reduce',function(){
    console.log('执行了reduce()');
    this.count--;
});
// 调用
function reduce() {
    // 事件调用
    console.log('emit事件调用');
    app.$emit('reduce');
}

// $once执行一次的事件
app.$once('reduceOnce',function(){
    console.log('只执行一次的方法');
    this.count--;
});
// 调用
function reduceOnce() {
    app.$emit('reduceOnce');
}

// 关闭事件
function off(){
    console.log('关闭事件');
    app.$off('reduce');
}
</script>
</body>
</html>
```

[参考教程](http://doc.liangxinghua.com/vue-family/1.html)
