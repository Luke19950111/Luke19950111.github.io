---
title: js原型的一些理解
date: 2019-10-11 15:21:06
tags: [原型, this, __proto__]
categories: JavaScript
---
<center>调原型中方法时怎样把当前对象传进去？</center>
<!-- more -->

声明一个对象，对象默认有`__proto__`属性，指向其原型

```javascript
let obj = {}

obj.__proto__ === window.Object.prototype   //true
```

改变 obj 的 `__proto__` 属性指向
```javascript
let 共有属性 = {
    hi(){
        console.log('hi')
    }
}
let obj = {}
obj.__proto__ = 共有属性

obj.hi()  //hi

```

调共有属性中的`hi`时，`hi`怎样获取到对象 obj？
obj 作为第一个参数传给 hi 了吗：

```javascript
let 共有属性 = {
    hi(a){
        console.log(a)
    }
}
let obj = {}
obj.__proto__ = 共有属性

obj.hi()  //undefined
```

实际上必须通过`this`拿到，或者理解为参数`a`前面还有一个看不见的参数`this`：

```javascript
let 共有属性 = {
    hi(/*this,*/a){
        console.log(this)
    }
}
let obj = {}
obj.__proto__ = 共有属性

obj.hi()  //{}
```

可以借助python理解一下：
python 显式的将第一个参数写为`self`，而javascript将`this`隐藏起来，但依然可以在调用方法时通过 this 将对象传入方法中。

```python
class MyClass:
    """A simple example class"""
    def hi(self):
        return self

x = MyClass()
x.hi()
```


