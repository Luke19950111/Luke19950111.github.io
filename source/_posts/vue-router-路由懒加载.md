---
title: vue-router：路由懒加载
date: 2019-06-19 15:30:51
tags: [vur-router, 懒加载]
categories:
- vue
- vue-router
---
<center>路由懒加载</center>
<!-- more --><br/>

# 正常路由模式

```
// 1、先引入页面组件
import Home from '@/components/Home'

// 2、使用组件
    {
        path: '/home',
        component: Home
}
```

# 懒加载模式

大项目中，使用懒加载模式提高页面初始化效率，三种写法：

```
component: (resolve) => require(['@/components/One'], resolve)
```

```
component: () => import('@/components/Two')
```

```
components: r => require.ensure([], () => r(require('@/components/Three')), 'group-home')
```

- 一般常用第二种简写
- 第三种中，'group-home'是把组件按组分块打包，可以将多个组件放入这个组中，在打包的时候Webpack会将相同chunk下的所有异步模块打包到一个异步块里面。 //???

[参考教程](http://doc.liangxinghua.com/vue-family/1.html)
