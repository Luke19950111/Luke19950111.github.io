---
title: vue-router：mode与404
date: 2019-06-19 15:10:28
tags: [vue-router, 404]
categories:
- vue
- vue-router
---
<center>喜闻乐见404</center>
<!-- more --><br/>

# mode模式

`mode`取值说明：

- `history`：URL不显示`#`，示例：http://localhost:8080/home
- `hash`：默认值，会多一个`#`，示例：http://localhost:8080/#/home

```
export default new Router({
    mode: 'history', //mode模式
    routes: [...]
})
```

# 404页面设置

如果访问路由不存在，或用户输入错误，给一个404提示页面。

- 在src/router/index.js中加入代码：

```
// 404
{
    path: '*',
    component: () => import('@/components/404')
}
```

- 编写src/components/404.vue页面：

```
<template>
    <div class="hello">
        <h1>404 not found</h1>
    </div>
</template>
<script>
export default {
    data () {
        return {

        }
    }
}
</script>

<style scoped>
</style>
```

[参考教程](http://doc.liangxinghua.com/vue-family/1.html)
