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