---
title: 写一个可拖曳的div
date: 2020-08-03 16:58:12
tags: [DOM]
categories: JavaScript
---

<center>用mouse事件写一个可拖曳的div</center>
<!-- more -->

```html
<div id='app'></div>
```

```css
#app{
  border: 1px solid red;
  height: 100px;
  width: 100px;
  position: absolute;
  top: 0;
  left: 0;
}
```

```js
let dragging = false
let position = null

let app = document.getElementById('app')
app.addEventListener('mousedown', e => {
  dragging = true
  position = [e.clientX, e.clientY]
})

document.addEventListener('mousemove', e => {
  if(dragging){
    const x = e.clientX
    const y = e.clientY
    const deltaX = x - position[0]
    const deltaY = y - position[1]
    const left = parseInt(app.style.left || 0)
    const top = parseInt(app.style.top || 0)
    app.style.left = left + deltaX + 'px'
    app.style.top = top + deltaY + 'px'
    position = [x, y]
  }
})

document.addEventListener('mouseup', e => {
  dragging = false
})
```
