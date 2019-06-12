---
title: call、apply、bind的用法
date: 2018-04-20 20:50:31
tags: [this, call, apply, bind]
categories: JavaScript
---
<center>call、apply、bind</center>
<!-- more -->

1. call、apply、bind都可以用来切换/固定 this 的指向；
2. call 
（1）call方法可以指定函数内部this的指向（即函数执行时所在的作用域），然后在所指定作用域中调用函数；
（2）call方法的第一个参数就是this所要指的对象（如果没有参数，或者第一个参数是undefined或null，则指向全局对象）；
（3）后面的参数是函数调用时所需的参数。
3. apply
（1）apply方法与call方法类似，也是改变this指向，唯一区别是它接受一个数组作为函数执行时参数；
（2）apply方法的第一个参数也是this所要指的那个对象，如果设为null或undefined，就指向全局对象；
（3）第二个参数是一个数组，数组成员依次作为参数传入原函数。
4. bind
（1）bind方法用于将函数体内this绑定到某个对象，然后返回一个新的函数；（apply方法和call方法都会立即执行函数）
（2）bind方法的参数就是所要绑定this的对象，如果第一个参数是null或undefined，绑定全局对象；
（3）bind方法还可以接受更多参数，将这些参数绑定原函数的参数。