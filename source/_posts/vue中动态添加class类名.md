---
title: vue中动态添加class类名
date: 2019-08-24 16:09:45
tags: [vue, class]
categories: vue
---
<center>点谁谁亮</center>
<!-- more -->

## 需求

有数组 `arr['a', 'b', 'c', 'd', 'e', 'f', 'g']`，需要渲染一组`button`，点谁谁亮

## 方法1：封装数组

封装数组，给数组中每项一个布尔值作为是否选中的状态

```
arr1[
    {name:'a', choosed:'true'},
    {name:'b', choosed:'false'},
    {name:'c', choosed:'false'},
    {name:'d', choosed:'false'},
    {name:'e', choosed:'false'},
    {name:'f', choosed:'false'},
    {name:'g', choosed:'false'},
]
```

循环渲染button

```
<ol>
  <li v-for="(item, index) in arr1">
    <button @click="onBtnClick(item)" :class="{choosed:item.choosed}">{{item.name}}</button>
  </li>
</ol>
```

点击事件中更改选中状态

```
onBtnClick(item){
    this.arr1.forEach((i)=>{
        i.choosed = false
    })
    item.choosed = true
}
```

choosed为类名

```
.choosed{
    background: red;
}
```

## 方法2：在标签上判断

循环渲染button

```
<ol>
  <li v-for="(item, index) in arr">
    <button @click="onBtnClick(item)" :class="{choosed:changeActive == item}">{{item}}</button>
  </li>
</ol>
```

默认给active一个值，写在data中，即默认选中a

```
data(){
    return{
        changeActive: 'a'
    }
}
```

点击事件中将item赋值给active，使当前所点击button的类名choose变为true，获得背景色

```
onBtnClick(item){
    this.changeActive = item
}
```

choosed为类名

```
.choosed{
    background: red;
}
```
