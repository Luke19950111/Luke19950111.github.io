---
title: 手写bind
date: 2020-04-18 15:18:53
tags: ['bind', 'polyfill', 'new', '原型链']
categories: JavaScript
---

<center>写个bind</center>
<!-- more -->

`bind` 位于 `Function.prototype` 上，我们来做一个polyfill，边测试变完成手写 `bind`。
新建目录bind，在其中创建src/index.js和test/index.js。之后操作都在bind目录中进行。

## fn.bind(asThis)

- 为了获取测试环境，以下代码以`bind2`作为手写的bind进行测试。
- 一个函数`fn1`调用`bind`方法，这个函数`fn1`就是`bind`中的`this`
- `fn1`调用`bind`方法时传入第一个参数`{name: 'lk'}`，返回一个函数`fn2`
- `fn2`调用时将这个参数`{name: 'lk'}`作为`this`调用`fn1`，返回结果
- 测试用例test/index.js中，函数`fn1`直接返回其`this`，应为调用`bind`时第一个参数`{name: 'lk'}`，所以断言`fn2().name === 'lk'`
- 执行`node test/index.js`，无报错，测试通过

```javascript
// src/index.js

function bind(asThis){
    //this就是调用bind的函数 此例为fn1
    const fn = this
    return function(){ //返回的函数fn2
        return fn.call(asThis)
    }
}

module.exports = bind

if(!Function.prototype.bind){
    Function.prototype.bind = bind
}
```
```javascript
// test/index.js

const bind = require("../src/index")
Function.prototype.bind2 = bind
console.assert(Function.prototype.bind2 !== undefined)

const fn1 = function () {
    return this
}

const fn2 = fn1.bind2({name: 'lk'})
console.assert(fn2().name === 'lk')
```

## fn.bind(asThis, param1, param2)

- 完善上一步代码，除了传入第一个参数作为 `this`，同时传入其他参数。改写代码如下
- 执行`node test/index.js`，无报错，测试通过

```js
// src/index.js

function bind(asThis, ...args){
    //this就是调用bind的函数
    const fn = this
    return function(){
        return fn.call(asThis, ...args)
    }
}

module.exports = bind

if(!Function.prototype.bind){
    Function.prototype.bind = bind
}
```

```js
// test/index.js

const bind = require("../src/index")
Function.prototype.bind2 = bind
console.assert(Function.prototype.bind2 !== undefined)

const fn1 = function (p1, p2) {
    return [this, p1, p2]
}

const fn2 = fn1.bind2({name: 'lk'}, 'a', 'b')
console.assert(fn2()[0].name === 'lk')
console.assert(fn2()[1] === 'a')
console.assert(fn2()[2] === 'b')
```

## fn.bind(asThis, param1, param2)(p3, p4)

- 继续完善，`fn1` 在调用 `bind` 时所返回的函数 `fn2` 在被调用时也传入参数
- 改写代码如下
- 执行`node test/index.js`，无报错，测试通过


```js
// src/index.js

function bind(asThis, ...args){
    //this就是调用bind的函数
    const fn = this
    return function(...args2){
        return fn.call(asThis, ...args, ...args2)
    }
}

module.exports = bind

if(!Function.prototype.bind){
    Function.prototype.bind = bind
}
```

```js
// test/index.js

const bind = require("../src/index")
Function.prototype.bind2 = bind
console.assert(Function.prototype.bind2 !== undefined)

const fn1 = function (...args) {
    return [this, ...args]
}

const fn2 = fn1.bind2({name: 'lk'}, 'a', 'b')
console.assert(fn2('c')[0].name === 'lk')
console.assert(fn2('c')[1] === 'a')
console.assert(fn2('c')[2] === 'b')
console.assert(fn2('c')[3] === 'c')
```

## 支持 `new` 的 `bind`

使用 `new` 调用函数时实际进行了以下操作：
```js
new fn(a)

var temp = {}  //1.生成一个临时对象
temp.__proto__ = fn.prototype  //2.在临时对象上改变它的原型链
fn.call(temp, a)  //3.把这个临时对象作为 this 调用 fn
return this  //4.把 fn 的 this 作为返回值return出来
```

原版的 `bind` 是支持 `new` 的

```js
//在浏览器控制台测试原版 bind

var fn = function(a){
    this.a = a 
}

new fn() //fn {a: undefined}
new fn('lk') //fn {a: "lk"}

var fn2 = fn.bind(undefined, 'lk2')
new fn2() //fn {a: "lk2"}
```

改写我们的 `bind` 使其支持 `new` 调用:

```js
// src/index.js

function bind(asThis, ...args){
    //this就是调用bind的函数
    const fn = this
    function resultFn(...args2){ //调用 bind 返回的函数 fn2
        //this //如果使用 new 调用fn2，会生成一个 this ；如果没有使用 new ， 此处的 this 为 global
        //this.__proto__ = resultFn.prototype //这个this的原型链就是 fn2 的原型链
        //以此条件判断是否用 new 调用的 fn2 
        return fn.call(
            // this.__prototype === resultFn.prototype ? this : asThis, 
            //instanceof 运算符用于检测构造函数的 prototype 属性是否出现在某个实例对象的原型链上
            this instanceof resultFn ? this : asThis,
            ...args, 
            ...args2
        )
    }
    resultFn.prototype = fn.prototype
    return resultFn
}

module.exports = bind

if(!Function.prototype.bind){
    Function.prototype.bind = bind
}
```

```js
// test/index.js

const bind = require("../src/index")
Function.prototype.bind2 = bind
console.assert(Function.prototype.bind2 !== undefined)

const fn1 = function (p1, p2) {
    this.p1 = p1
    this.p2 = p2
}

const fn2 = fn1.bind2(undefined, 'a', 'b')
const object = new fn2()

console.assert(object.p1 === 'a')
console.assert(object.p2 === 'b')
```

## 使用旧的API

现在手写的 `bind2` 已经实现了原版 `bind` 的功能。因为是在做 `polyfill`，不应该用到所有跟 `bind` 同时出现的所有新的api，同时必须进行错误处理。使用旧的语法修改代码：

```js
// src/index.js

var slice = Array.prototype.slice
function bind(asThis){
    // this就是调用bind的函数
    var args = slice.call(arguments, 1)
    var fn = this
    if(typeof fn !== 'function'){
        throw new Error('Function.prototype.bind - ' +
        'what is trying to be bound is not callable')
    }
    function resultFn(){
        var args2 = slice.call(arguments, 0)
        return fn.apply(
            resultFn.prototype.isPrototypeOf(this) ? this : asThis,
            args.concat(args2)
        )
    }
    resultFn.prototype = fn.prototype
    return resultFn

}

module.exports = bind

if(!Function.prototype.bind){
    Function.prototype.bind = bind
}
```

[MDN bind](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Function/bind)


完