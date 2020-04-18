---
title: 手写bind
date: 2020-04-18 15:18:53
tags: ['bind']
categories: JavaScript
---

<center>写个bind</center>
<!-- more -->

`bind` 位于 `Function.prototype` 上，我们来做一个polyfill，边测试变完成手写 `bind`。
新建目录bind，在其中创建src/index.js和test/index.js。之后操作都在bind目录中进行。

## fn.bind(asThis)

-为了获取测试环境，我们以`bind2`作为手写的bind进行测试。
- 一个函数`fn1`调用`bind`方法，这个函数`fn1`就是`bind`中的`this`
- `fn1`调用`bind`方法时传入第一个参数`{name: 'lk'}`，返回一个函数`fn2`
- `fn2`调用时将这个参数`{name: 'lk'}`作为`this`调用`fn1`，返回结果
- 测试用例test/index.js中，函数`fn1`直接返回其`this`，应为调用`bind`时第一个参数`{name: 'lk'}`，所以断言`fn2().name === 'lk'`
- 执行`node test/index.js`，无报错，测试通过

src/index.js
```javascript
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
test/index.js
```javascript
const bind = require("../src/index")
Function.prototype.bind2 = bind
console.assert(Function.prototype.bind2 !== undefined)

const fn1 = function () {
    return this
}

const fn2 = fn1.bind2({name: 'lk'})
console.assert(fn2().name === 'lk')
```

