---
title: vue-router：页面跳转
date: 2019-06-18 10:54:18
tags: [vue-router, vue-cli]
categories:
- vue
- vue-router
---
<center>vue-router页面跳转</center>
<!-- more -->

# <router-link> 标签跳转

`to`：导航路径

```
<p>导航 ：
   <router-link to="/">首页</router-link>
   <router-link to="/hello">hello</router-link>
</p>
```

# JS代码内跳转

```
this.$router.push('/xxx')
```

具体简单用法：
- 先编写一个按钮，在按钮上绑定`goHome`方法

```
<button @click='goHome'>回到首页</button>
```

- 在`<script>`模块的`methods`里加入`goHome`方法，并用`this.$router.push('/')`导航到首页

```
export default {
    name: 'app',
    methods: {
        goHome(){
            this.$router.push('/home');
        }
    }
}
```

# 其他

```
//  后退一步记录，等同于 history.back()
this.$router.go(-1)
// 在浏览器记录中前进一步，等同于 history.forward()
this.$router.go(1)
```

[参考教程](http://doc.liangxinghua.com/vue-family/1.html)