---
title: vue计算属性
date: 2020-01-01 15:22:07
tags: ['computed']
categories: vue
---
<center>vue计算属性vs方法</center>
<!-- more -->

***

## 计算属性vs方法

使用计算属性例子：
```html
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>
```
```javascript
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // 计算属性的 getter
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('')
    }
  }
})
```
上面例子中声明了一个计算属性 `reversedMessage`，Vue 知道 `vm.reversedMessage` 依赖于 `vm.message`，因此当 `vm.message` 发生改变时，所有依赖 `vm.reversedMessage` 的绑定也会更新。

通过在表达式中调用方法达到同样的目的：
```html
<p>Reversed message: "{{ reversedMessage() }}"</p>
```
```javascript
// 在组件中
methods: {
  reversedMessage: function () {
    return this.message.split('').reverse().join('')
  }
}
```

- 这两种方法的最终结果是完全相同的。
- **计算属性是基于它们的响应式依赖进行缓存的**。只在相关响应式依赖发生改变时它们才会重新求值。这就意味着只要 `message` 还没有发生改变，多次访问 `reversedMessage` 计算属性会立即返回之前的计算结果，而不必再次执行函数。
- 相比之下，每当触发重新渲染时，调用方法将**总会**再次执行函数。
- 如果不希望有缓存，可以使用方法代替计算属性。

