---
title: element-ui中el-select的change事件获取对象多个值
date: 2020-05-13 16:52:56
tags: ['element-ui', 'el-select']
categories: vue
---

<center>el-select change 事件自定义传参</center>
<!-- more -->

```html
<el-select v-model="item.name" placeholder="请选择" @change="onChange">
   <el-option
       v-for="item in arr"
       :key="item.id"
       :label="item.name"
       :value="item.id"
    >
    </el-option>
</el-select>
```

```js
arr: [
    {id: 1, name: 'lk1'},
    {id: 2, name: 'lk2'},
    {id: 3, name: 'lk3'}
]
```

```js
onchange(val){
    var obj = {}
    obj = this.arr.find(function(item){
        retrun item.id === val
    })
    console.log(obj.name)
    console.log(obj.id)
}
```


## 补充

给 `el-select` 加上 `value-key` 属性，值为数据源数组元素中**唯一键**
例如数据源数组
```js
[
    {id: 1, name: 'lk1', age: 18},
    {id: 2, name: 'lk2', age: 19},
    {id: 3, name: 'lk3', age: 20}
]
```
那么可以设置：
```html
<el-select v-model="value" value-key="id" placeholder="请选择">
```
此时 `value` 绑定的值就是所选项整个对象。