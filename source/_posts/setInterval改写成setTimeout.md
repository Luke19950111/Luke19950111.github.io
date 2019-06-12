---
title: setInterval改写成setTimeout
date: 2018-05-25 02:44:00
tags: [setInterval(), setTimeout()]
categories: JavaScript
---
<center>好处就是可以更改延迟时间</center>
<!-- more -->

`setInterval()`方法重复调用一个函数或执行一个代码段，在每次调用之间有固定的时间延迟。
`setTimeout()`方法设置一个定时器，定时器到期后执行一个函数或一段代码。

```
let n = 0
let id = setInterval ( () => {
  n += 1
  console.log(n)
}, 1000)
```
上面代码每秒打印一次`n`。但是由于只读取一次时间延迟，设置好以后无法更改时间延迟。
用`setTimeout()`来改写`setInterval()`可以解决这个问题。

```
let duration = 1000
let n = 0
let id = setTimeout( function runAgain(){
  n += 1
  console.log(n)
  setTimeout(runAgain, duration)
}, duration)
```
上面代码可以通过改变duration的值来改变时间延迟。`setTimeout`每次执行都会读取延迟时间，所以改变时间能立即生效。