---
title: 创建一个支持v-model的自定义输入组件
date: 2020-01-13 16:29:56
tags: ['自定义组件','v-model']
categories: vue
---
<center>自定义输入组件v-model</center>
<!-- more -->

***

组件内`<input>`必须：
- 将其 `value` 特性绑定到一个名叫 `value` 的prop上
- 在其 `input` 事件被触发时，将新的值通过自定义的 `input` 事件抛出

代码：
```javascript
Vue.component('custom-input', {
  props: ['value'],
  template: `
    <input
      v-bind:value="value"
      v-on:input="$emit('input', $event.target.value)"
    >
  `
})
```

```html
<custom-input v-model="searchText"></custom-input>
```

[vue文档](https://cn.vuejs.org/v2/guide/components.html#%E5%9C%A8%E7%BB%84%E4%BB%B6%E4%B8%8A%E4%BD%BF%E7%94%A8-v-model)