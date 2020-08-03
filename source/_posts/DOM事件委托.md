---
title: DOM事件委托
date: 2020-08-03 15:10:34
tags: [DOM, 事件委托]
categories: JavaScript
---

<center>理解一下事件委托</center>
<!-- more -->

## 简单理解

举个例子，要监听 `ul` 中 `li` 上的 `click` 事件，但 `li` 是动态生成的，可能还不存在，于是监听事件委托给父元素 `ul` ：

```js
ul.addEventListener("click", function (e) {
    if (e.target.tagName.toLowerCase() === "li") {
        fn(); // 执行某个函数
    }
});
```

但是这样存在问题：如果 `li` 里还有子元素 `span`，点击的是 `li` 里的 `span`， 无法触发 fn，显然不对。

## 完整代码
思路是遍历所点击元素的祖先元素，看其中有没有需要触发事件的元素：

```js
function delegate(element, eventType, selector, fn) {
    element.addEventListener(eventType, (e) => {
        let el = e.target;
        while (!el.matches(selector)) {
            if (element === el) {
                el = null;
                break;
            }
            el = el.parentNode;
        }
        el && fn.call(el, e, el);
    });
    return element;
}
```
