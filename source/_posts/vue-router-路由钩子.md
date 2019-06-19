---
title: vue-router：路由钩子
date: 2019-06-19 11:27:30
tags: [vue-router, 路由钩子]
categories:
- vue
- vue-router
---
<center>路由拦截器</center>
<!-- more --><br/>

路由钩子，即路由拦截器，vue-router一共有三类：
- 全局钩子
- 路由单独钩子
- 组件内钩子

# 全局钩子

`router.beforeEach()`

在src/router/index.js中写代码：

```
// 定义路由配置
const router = new Router({ ... })

// 全局路由拦截-进入页面前执行
router.beforeEach((to, from, next) => {
    // 这里可以加入全局登陆判断
    // 继续执行
    next();
});

// 全局后置钩子-常用于结束动画等
router.afterEach(() => {
    //不接受next
});

export default router;
```

每个钩子方法接收三个参数：
- `to`: Route : 即将要进入的目标 [路由对象]
- `from`: Route : 当前导航正要离开的路由
- `next`: Function : 继续执行函数
  - next()：继续执行
  - next(false)：中断当前导航
  - next('/')或next({path:'/'})：跳转新页面，常用于登陆失效跳转登陆

# 路由单独钩子

`router.beforeEnter()`

在路由配置中单独加入钩子，在src/router/index.js中使用：

```
{
    path:'/home/one', // 子页面1
        component: One,
        // 路由内钩子
        beforeEnter: (to, from, next) => {
        console.log('进入前执行');
            next();
        }
}
```

# 组件内钩子

在路由组件内定义钩子函数（下面例子在组件`Two.vue`中使用）：

- `beforeRouteEnter`：进入页面前调用
- `beforeRouteUpdate` (2.2 新增)：页面路由改变时调用，一般需要带参数
- `beforeRouteLeave`：离开页面调用

```
<script>
export default {
    name: 'Two',
    data () {
        return {
            msg: 'Hi, I am Two Page!'
        }
    },
    // 进入页面前调用
    beforeRouteEnter(to, from, next) {
        console.log('进入enter路由钩子')
        next()
    },
    // 离开页面调用
    beforeRouteLeave(to,from, next){
        console.log('进入leave路由钩子')
        next()
    },
    // 页面路由改变时调用
    beforeRouteUpdate(to, from, next) {
        console.log('进入update路由钩子')
        console.log(to.params.id)
        next()
    }
}
</script>
```

[参考教程](http://doc.liangxinghua.com/vue-family/1.html)
