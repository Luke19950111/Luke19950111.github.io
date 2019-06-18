---
title: 'vue-router：子路由-路由嵌套'
date: 2019-06-18 10:22:14
tags: [vue-router, vue-cli, 路由]
categories:
- vue
- vue-router
---
<center>vue-router中的子路由</center>
<!-- more -->

- 子路由，也叫路由嵌套，在`children`后跟路由数组来实现；
- 数组里和其他配置路由基本相同，需要配置`path`和`component`；
- 在相应部分添加`<router-view/>`来展现子页面信息，相当于嵌入`iframe`。

# 父页面 (src/components/Home.vue) 

```
<template>
    <div class="hello">
        <h1>{{ msg }}</h1>
        <!-- 添加子路由导航 -->
        <p>导航 ：
            <router-link to="/home">首页</router-link> | 
            <router-link to="/home/one">-子页面1</router-link> |
            <router-link to="/home/two">-子页面2</router-link>
        </p>
        <!-- 子页面展示部分 -->
        <router-view/>
    </div>
</template>

<script>
export default {
    name: 'Home',
    data () {
        return {
            msg: 'Home Page!'
        }
    }
}
</script>

<style scoped>
</style>
```

# 子页面1 (src/components/one.vue)

```
<template>
    <div class="hello">
        <h1>{{ msg }}</h1>
    </div>
</template>
<script>
export default {
    name: 'One',
    data () {
        return {
            msg: 'Hi, I am One Page!'
        }
    }
}
</script>

<style scoped>
</style>
```

# 子页面2 (src/components/two.vue)

```
<template>
    <div class="hello">
        <h1>{{ msg }}</h1>
    </div>
</template>
<script>
export default {
    name: 'Two',
    data () {
        return {
            msg: 'Hi, I am Two Page!'
        }
    }
}
</script>

<style scoped>
</style>
```

# 路由配置 (src/router/index.js)

```
import Vue from 'vue'
import Router from 'vue-router'
import Home from '@/components/Home'
import One from '@/components/one' 
import Two from '@/components/two'

Vue.use(Router)

export default new Router({
    routes: [
    {
        path: '/', // 默认页面重定向到主页
        redirect: '/home'
    },
    {
        path: '/home', // 主页路由
        name: 'Home',
        component: Home,
        children:[ // 嵌套子路由
            {
                path:'one', // 子页面1
                component:One
            },
            {
                path:'two', // 子页面2
                component:Two
            },
        ]
    }
    ]
})
```

[参考教程](http://doc.liangxinghua.com/vue-family/1.html)