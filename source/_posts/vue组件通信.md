---
title: vue组件通信
date: 2019-06-22 15:58:00
tags: [props, $emit]
categories: vue
---
<center>vue父子组件直接通信，爷孙组件经由中间父组件进行通信</center>
<!-- more --><br/>

# 父子通信

## 父组件传值给子组件

- 在父组件中，在引用子组件的标签上，通过属性传值给子组件

- 在子组件中，通过`props`接收父组件传来的值，`props`是一个数组，数组里的值要用字符串包裹（也可以是对象）

- 子组件就可以直接使用父组件传来的值了

## 子组件与父组件通信

- 子组件使用`$emit`触发父组件中的事件 `vm.$emit( eventName, […args] )` 附加参数都会传给监听回调

- 父组件中，在引用子组件的标签上监听子组件`$emit`的事件

***

- HTML代码

```
<body>
  <div id="app">
    <p>{{ message }}</p>
    <button @click='visible=true'>打开</button>
    <child v-show='visible' @close='visible=false' :xxx='child'></child>   
    //子组件中button被点击时触发父组件此处close事件
    //子组件的引用，:xxx='child' 属性将父组件中的child值传给子组件
  </div>

</body>
```
- JS代码

```
Vue.component('child', {
  props: ['xxx'],  // 子组件child中接收父组件传来的值 xxx
  template: `
    <div>
        {{xxx}}  //子组件就可以直接使用父组件传来的值了
        <button @click="$emit('close')">关闭</button>  //点击button触发父组件中引用该子组件的标签上的close事件
    </div>`
})

new Vue({
  el: '#app',
  data: {
    message: '父组件',
    visible: false,
    child: '子组件'
  }
})
```

# 爷孙通信

- 爷孙不能直接通信，要经由中间的父组件通信

- 下面代码示例中，点击爷爷的“打开”button，爷爷改变`visible`的值并传给父亲，父亲再根据这个值控制孙子显示，这样就实现了爷爷对孙子显示的控制

- 点击孙子的“关闭”button，孙子触发父亲的`frandsonClose`事件，进而触发爷爷的`close`事件，实现的孙子对爷爷的通信

```
// 父组件html（这是爷爷）

<body>
  <div id="app">
    <p>{{ message }}</p>
    <button @click='visible=true'>打开</button>
    <child :xxx='visible' @close='visible=false'></child>
  </div>
</body>
```

```
// 子组件（这是父亲）
Vue.component('child', {
  props: ['xxx'],
  template: `
    <div>
        子组件    
        <grandson v-show='xxx' @grandsonClose="$emit('close')"></grandson>
    </div>`
})

// 孙组件（这是孙子）
Vue.component('grandson', {
  template: `
    <div>
        孙组件
        <button @click="$emit('grandsonClose')">关闭</button>
    </div>`
})

//这是爷爷的
new Vue({
  el: '#app',
  data: {
    message: '父组件',
    visible: false,
  }
})
```

[参考博文](https://www.cnblogs.com/padding1015/p/7878710.html)
