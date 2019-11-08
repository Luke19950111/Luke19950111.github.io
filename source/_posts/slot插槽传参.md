---
title: slot插槽传递值
date: 2019-11-08 15:54:59
tags: ['插槽', 'slot', 'scope', '组件', 'props']
categories: vue
---
<center>向插槽中传参</center>
<!-- more -->

***
## 需求
- 有组件 `full-page`
- 组件 `full-page` data中有值 `state`
- 组件 `full-page` 中有一个插槽
- 有组件 `page1`，将组件 `page1` 插入到组件 `full-page` 的插槽中
- 同时要将组件 `full-page` 中的 `state` 传入组件 `page1` 中

## 代码

- 在组件`full-page`中向插槽传值
```
<template>
    <div>
        <slot :state="state></slot>
    </div>
</template>
<script>
    export default{
        data(){
            return{
                state: '1'
            }
        }
    }
</script>

```

- 使用组件`full-page`时将值传入插槽中
```html
<full-page>
    <template scope="props">
        <page1 :state="props.state"><page1>
    </template>
</full-page>
```

- 在组件`page1`中获取值
```html
<script>
    export default{
        props: ['state']
        data(){
            return{
                
            }
        }
    }
</script>
```

[demo代码](https://github.com/Luke19950111/fullpage-demo)