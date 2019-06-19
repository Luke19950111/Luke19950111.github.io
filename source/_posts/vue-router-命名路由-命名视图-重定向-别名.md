---
title: vue-router：命名路由-命名视图-重定向-别名
date: 2019-06-19 11:53:38
tags: [vue-router, 命名, 重定向]
categories:
- vue
- vue-router
---
<center>命名路由，命名视图，重定向，别名</center>
<!-- more --><br/>

# 命名路由

给一个路由命一个唯一的名称，跳转时调用这个名称即可。

- 在src/router/index.js中加一个带`name`的路由

```
{
    path: 'one', // 子页面1
    name: 'one', // 路由名称-命名路由
    component: One // 页面组件
}
```

- 在src/components/Home.vue页面中调用

```
// template跳转调用
<router-link :to="{name: 'one'}">子页面1</router-link>

// router.push函数跳转调用
router.push({ name: 'user'}})
```

# 命名视图

在同一个页面展示多个视图，如果不用嵌套，只能采用命名视图实现。

- 在src/router/index.js中

命名视图只能放在最顶级页面中，下面的代码不能放在其他页面。

```
import Vue from 'vue'
import Router from 'vue-router'
// 创建页面组件
const Header = { template: '<div>Header</div>' }
const Left = { template: '<div>Left</div>' }
const Right = { template: '<div>Right</div>' }

Vue.use(Router)

export default new Router({
    routes: [
    {
        path: '/', // 主页路由
        components: {
            default: Header,
            a: Left,
            b: Right
        }
    }
    ]
})
```

- 在src/App.vue中

```
<template>
    <div id="app">
        <router-view />
        <router-view name="a" class="left" />
        <router-view name="b" class="right" />
    </div>
</template>

<script>
export default {
    name: 'App'
}
</script>

<style>
#app {
    text-align: center;
    color: #2c3e50;
    width: 500px;
    border: 1px solid red;
    margin: 0 auto;
}

.left,.right{
    float: left;
    width:48%;
    text-align: center;
    border:1px solid red
}
</style>
```

# 重定向

重定向通过router配置中的关键词`redirect`实现

- 在src/router/index.js中

```
export default new Router({
    routes: [
    {
        path: '/', // 默认页面重定向到主页
        redirect: '/home' // 重定向
    },
    {
        path: '/home', // 主页路由
        component: Home,
        children:[ // 嵌套子路由
            {
                path:'/home/two/:id/:name', // 子页面2
                component:Two
            },
            {
                path:'/home/three/:id/:name', // 子页面3
                name: 'three', // 路由名称-命名路由
                redirect: '/home/two/:id/:name' // 重定向-传递参数
            },
        ]
    }
    ]
})
```

- 在src/components/Home.vue中

```
<router-link to="/">首页</router-link> | 
<router-link to="/home/two/1/张三">子页面2</router-link>  |
<router-link :to="{name: 'three', params: {id: 2, name: 'mu'}}">子页面3</router-link>
```

点击‘子页面3’时带着参数重定向到子页面2

- 不带参数重定向

```
redirect: '/home' // 重定向-不带参数
```

- 带参数重定向

```
redirect: '/home/two/:id/:name' // 重定向-传递参数
```

# 别名

重定向是通过route的配置中关键词`alias`来实现的。

- 在src/router/index.js中

```
{
    path:'/one', // 子页面1
    component:One,
    alias: '/oneother'
}
```

- 在src/components/Home.vue页面中

```
<router-link to="/oneother">子页面1</router-link>
```

`redirect`和`alias`的区别：

- redirect: 直接改变了url的值，把url变成真实的path路径；
- alias: url路径没有改变，让用户知道自己访问的路径，只改变`<router-view>`中的内容。

别名不能用在path为`/`中，下面代码别名不起作用：

```
{
    path: '/',
    component: Hello,
    alias:'/home'
}
```

[参考教程](http://doc.liangxinghua.com/vue-family/1.html)
