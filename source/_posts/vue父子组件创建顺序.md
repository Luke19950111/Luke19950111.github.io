---
title: vue父子组件创建顺序
date: 2023-02-14 18:11:51
tags: [生命周期, Lifecycle]
categories: vue
---

<center>使用 v-if 延后子组件创建</center>

<!-- more -->

## 业务问题

业务中遇到一个场景：
  - 父组件创建时异步请求接口，将结果存入store中（父-mounted）
  - 子组件创建时异步请求接口，从store中获取上一步父组件存入的数据作为参数（子-mounted）
  - 子组件先创建，请求时拿不到参数

解决方法：
  - 子组件上使用`v-if`，父组件获取数据后再渲染子组件

## 父子组件创建顺序

```html
<!-- 父组件 -->
<script>
import Child from './child.vue'
export default {
  components: {
    Child
  },
  beforeCreate () {
    console.log('父-beforeCreate')
  },
  created () {
    console.log('父-created')
  },
  beforeMount () {
    console.log('父-beforeMount')
  },
  mounted () {
    console.log('父-mounted')
  }
}
</script>
<template>
  <Child></Child>
</template>
```
```html
<!-- 子组件 -->
<script>
export default {
  beforeCreate () {
    console.log('子-beforeCreate')
  },
  created () {
    console.log('子-created')
  },
  beforeMount () {
    console.log('子-beforeMount')
  },
  mounted () {
    console.log('子-mounted')
  }
}
</script>
<template>
  <div>child</div>
</template>
```
打印结果：
![](lifecycle-console.png)
