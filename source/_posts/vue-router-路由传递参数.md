---
title: vue-router-路由传递参数
date: 2019-06-18 14:57:57
tags: [vue-router, vue-cli, 参数]
categories:
- vue
- vue-router
---
<center>vue-router中父页面通过路由给子页面传递参数</center>
<!-- more -->

# 通过<router-link>标签中的 to 传参

基本语法：

```
<router-link :to="{name:'xxx', params:{key:value}}">子页面</router-link>
```

- `to`前面带冒号，后面跟一个对象形式的字符串
- `name`：路由配置文件src/router/index.js中起的name值，叫做命名路由
- `params`：要传的参数，是对象形式，可以在对象里传多个值

具体实例：可以参照 [子路由-路由嵌套](https://luke19950111.github.io/2019/06/18/vue-router-%E5%AD%90%E8%B7%AF%E7%94%B1-%E8%B7%AF%E7%94%B1%E5%B5%8C%E5%A5%97/) 中的代码修改

- 在父页面src/components/Home.vue里面导航中添加如下代码：

```
<router-link :to="{name:'one', params:{username: 'test'}}">子页面1</router-link>
```

- 在路由配置文件src/router/index.js里添加以下代码，重点是`name`：

```
{
    path: 'one', //子页面1
    name: 'one', //路由名称-命名路由
    component: One
}
```

- 在子页面src/components/One.vue里接收参数：

```
<h2>{{$route.params.username}}</h2>
```

# url中传递参数

- 在路由中以冒号传递，在路由配置文件src/router/index.js里添加代码：

```
{
    path: '/home/two/:id/:name', //子页面2，如果写在home的children中则不用写/home
    component: Two
}
```

- 在父页面src/components/Home.vue中写入参数：

```
<router-link to="home/two/1/木子">子页面2</router-link>
```

`to`前面没有冒号，字符串路由，必须全部匹配

- 在子页面src/components/Two.vue中接收参数：

```
<p>ID: {{$route.params.id}}</p>
<p>name: {{$route.params.name}}</p>
```

- 如果路由参数有特定规则，需要加入正则表达式：

```
{
    path:'/home/two/:id(\\d+)/:name', // 子页面2
    component:Two
}
```

# params传递参数

- 在路由配置文件src/router/index.js里添加代码：

```
{
    path:'/home/three', // 子页面3
    name: 'three',
    component:Three
}
```

- 在父页面src/components/Home.vue中写入参数：

```
//template
<button @click="toThreePage">向子页面3params传参</button>

//script
methods: {
    toThreePage(){
        this.$router.push({name:'three', params:{id:1, name:'木子'}})
    }
}
```

- 在子页面src/components/Three.vue中接收参数：

```
<p>ID: {{$route.params.id}}</p>
<p>name: {{$route.params.name}}</p>
```

- 动态路由使用`params`传递参数，在`this.$router.push()`方法中`path`不能和`params`一起使用，否则`params`无效，需要用`name`指定页面；
- 以上方式参数不会显示在浏览器地址栏中，刷新后就获取不到参数了。改进：在路由配置文件中改代码

```
{
    path:'/home/three/:id/:name', // 子页面3
    name: 'three',
    component:Three
}
```

# query传递参数

- 在路由配置文件src/router/index.js里添加代码：

```
{
    path:'/home/three', // 子页面3
    name: 'three',
    component:Three
}
```

- 在父页面src/components/Home.vue中写入参数：

```
//template
<button @click="toThreePage">向子页面3query传参</button>

//script
methods: {
    toThreePage(){
        this.$router.push({path:'home/three', query:{id:1, name:'木子'}})
    }
}
```

- 在子页面src/components/Three.vue中接收参数：

```
<p>ID: {{$route.query.id}}</p>
<p>name: {{$route.query.name}}</p>
```

- 动态路由使用`query`传递参数，会显示在浏览器地址栏中，以上链接为`/home/three?id=1&name=木子`

[参考教程](http://doc.liangxinghua.com/vue-family/1.html)
