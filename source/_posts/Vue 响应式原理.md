---
title: Vue 响应式原理
date: 2023-08-09 18:14:13
tags: ['响应式', 'Dep', 'Watcher', 'observe', 'Observer', 'defineReactive']
categories: vue
---

{% asset_img Dep.png 阅读源码理解响应式原理 %}
<!-- more -->

# Vue2
[Vue2 响应式实现](https://github.com/Luke19950111/vue2-data-reactive)

## 将对象属性转为响应式

首先需要将普通对象转为每个层级的属性都是响应式（可被侦测的）的对象，用到三个方法和类：
- 函数 observe：接收对象，判断是否有 `__ob__` 属性（Observer类的实例），如果有直接返回该属性，没有则创建 `Observer` 实例返回
- Observer 类：遍历对象属性执行 `defineReactive`，将一个普通 object 每个层级的属性都变为响应式
- 函数 defineReactive：使用 `Object.defineProperty` 将对象属性变为 getter、setter，并对该子属性也调用 `observe` 函数

由此形成递归，这个递归不是自己调用自己，而是由 observe、Observer、defineReactive 三个函数和类循环调用形成（`observe => Observer => defineReactive => observe... `）

递归调用如下图示：
![](递归调用.png)

简单代码如下：

```js
// observe.s

import Observer from './Observer.js'

export default function observe (value) {
  if (typeof value !== 'object') return
  var ob
  if (typeof value.__ob__ !== 'undefined') {
    ob = value.__ob__
  } else {
    ob = new Observer(value)
  }
  return ob
}
```
```js
// Observe.js

import { def } from './utils.js'
import defineReactive from './defineReactive.js'

export default class Observer {
  constructor (value) {
    // 将 Observer 实例设为 value 的 __ob__ 属性
    def(value, '__ob__', this, false)
    // Observer 类的目的是：将一个普通的 object 每个层级的属性都变为响应式
    this.walk(value)
  }
  walk (value) {
    for (let k in value) {
      defineReactive(value, k)
    }
  }
}
```
```js
// defineReactive.js

import observe from './observe.js'
 export default function defineReactive (data, key, val) {
  console.log('defineReactive', key)
  if (arguments.length == 2) {
    val = data[key]
  }

  // 对子元素调用 observe，形成递归。这个递归不是自己调用自己，而是多个函数、类循环调用形成（observe => Observer => definReactive => observe...）
  let childOb = observe(val)

  Object.defineProperty(data, key, {
    enumerable: true,
    configurable: true,
    get() {
      return val
    },
    set(newValue) {
      if (val === newValue) {
        return
      }
      val = newValue
      // 当设置了新值，新值也要被 observe，新值可能也是一个对象
      childOb = observe(newValue)
    }
  })
 }
```


## 数组的响应式处理

- 上文写到在 `Observer` 类中遍历对象属性，调用 defineReactive，进而使用 Object.defineProperty 将其转为 getter、setter
- 对于数组类型，在 Observer 类中直接遍历其元素调用 observe 函数，形成递归
- 将七个会改变原数组的方法改写，并插入到原型链中
  - 以 Array.prototype 为原型创建对象 arrayMethods： `const arrayMethods = Object.create(arrayPrototype)`
  - 在 `arrayMethod` 对象上改写七个数组方法
  - 在 Observer 类里判断，如果处理的属性是数组，将改数组的原型改为 arrayMethods：`Object.setPrototypeOf(arr, arrayMethods)` 相当于 `arr.__proto__ = arrayMethods`
  - push、unshift、splice 可能往数组里插入新元素，需要对新元素也调用 observe

改变数组原型链：
![](改写数组方法.png)

现在代码如下：
```js
// Observer.js

import { def } from './utils.js'
import defineReactive from './defineReactive.js'
import { arrayMethods } from './array.js'
import observe from './observe.js'

export default class Observer {
  constructor (value) {
    // 将 Observer 实例设为 value 的 __ob__ 属性
    def(value, '__ob__', this, false)
    // Observer 类的目的是：将一个普通的 object 每个层级的属性都变为响应式
    if (Array.isArray(value)) {
      // 如果是数组，将数组原型指向改写数组方法后的 arrayMethods
      Object.setPrototypeOf(value, arrayMethods)
      this.observeArray(value)
    } else {
      this.walk(value)
    }
  }
  // 遍历对象属性
  walk (value) {
    for (let k in value) {
      defineReactive(value, k)
    }
  }
  // 遍历数组元素
  observeArray (arr) {
    // 不直接写 i < arr.length，避免特殊情况遍历过程中数组长度变化
    for (let i = 0, l = arr.length; i < l; i++) {
      observe(arr[i])
    }

  }
}
```
```js
// array.js

import { def } from './utils.js'

// 以 Array.prototype 为原型创建对象 arrayMethods
const arrayPrototype = Array.prototype
export const arrayMethods = Object.create(arrayPrototype)

// 要被改写的7个数组方法
const methodsNeedChange = [
  'push',
  'pop',
  'shift',
  'unshift',
  'splice',
  'sort',
  'reverse'
]

methodsNeedChange.forEach(methodName => {
  // 备份原来的方法
  const original = arrayPrototype[methodName]
  // 定义新的方法
  def(arrayMethods, methodName, function () {
    
    // 最外层传入 observe 的 data 一定是对象，第一次遍历 data 对象时给所有的第一层根属性都调用了 observe，所以数组上一定有 __ob__
    const ob = this.__ob__
    // push、unshift、splice 能够插入新元素，要把插入的新元素也 observe
    const args = [...arguments]
    let inserted = []
    switch (methodName) {
      case 'push':
      case 'unshift':
        inserted = args
        break
      case 'splice':
        inserted = args.slice(2)
        break
    }
    if (inserted.length) {
      ob.observeArray(inserted)
    }

    console.log(`调用改写后的数组方法${methodName}`)
    const result = original.apply(this, arguments) // this 就是调用方法的数组
    return result
  }, false)
})
```

## Watcher类和Dep类

通过`Watcher`类和`Dep`类实现依赖收集、添加订阅、通知更新

- Dep 类：把依赖收集的代码封装成一个 Dep 类，用来管理依赖，每个 Observer 的实例，属性中都有一个 Dep 的实例 (`__ob__.dep`)
- Watcher 类：Watcher 是一个中介，数据发生变化时通过 Watcher 中转，通知组件

依赖是什么？

依赖就是用到数据的地方，什么地方用到数据，什么地方就叫依赖。
Vue2 中，每个**组件**有对应的数据，并且每个组件有自己的 Watcher 实例，依赖就是这个 Watcher 实例。

何时收集，何时触发？

在 getter 中收集依赖，在 setter 中触发依赖；
Watcher 实例把自己设置到全局的一个指定位置 `Dep.target`，然后读取数据，触发数据的 getter；
用 Dep.target 是否存在，判断是否处于依赖收集阶段；
getter 中会从 `Dep.target` 变量读取**正在读取数据的 Watcher 实例**，把这个 Watcher 实例收集到 Dep 中；
Dep 使用**发布订阅模式**，当数发生变化，会循环依赖列表，把列表中所有的 Watcher 实例都通知一遍。
`getter => dep.depend(); setter => dep.notify(); 数组用 __ob__.dep.notify()`

图示如下：
![](Dep.png)

代码：
```js
// Dep.js

var uid = 0
export default class Dep {
  constructor() {
    this.id = uid++

    // 每个 dep 实例用数组存储自己的订阅者，每个订阅者都是 Watcher 的实例
    this.subs = []
  }
  // 添加订阅
  addSub(sub) {
    this.subs.push(sub)
  }
  // 通知更新
  notify() {
    console.log('notify')

    // 浅拷贝
    const subs = this.subs.slice()
    for (let i = 0, l = subs.length; i < l; i++) {
      subs[i].update()
    }
  }
  // 添加依赖
  depend() {
    // Dep.target 只是一个指定的全局位置，用 window.target 也行，只要是全局唯一
    if (Dep.target) {
      this.addSub(Dep.target)
    }
  }
}
```
```js
// Watcher.js

import Dep from "./Dep.js"
var uid = 0
export default class Watcher {
  constructor(target, expression, callback) {
    this.id = uid++
    this.target = target
    this.getter = parsePath(expression)
    this.callback = callback
    this.value = this.get()
  }
  get() {
    // 进入依赖收集阶段。将当前 watcher 实例设置到 Dep.target，Dep.target 有值则进入依赖收集阶段
    Dep.target = this
    
    const obj = this.target
    var value
    try {
      value = this.getter(obj)
    } finally {
      Dep.target = null
    }
    return value
  }
  update() {
    this.run()
  }
  run() {
    this.getAndInvoke(this.callback)
  }
  getAndInvoke(cb) {
    const value = this.get()

    if (value !==this.value || typeof value == 'object') {
      const oldValue = this.value
      this.value = value
      cb.call(this.target, value, oldValue)
    }
  }
}

function parsePath(str) {
  // a.b.c.d
  var segments = str.split('.')

  return (obj) => {
    for (let i = 0; i < segments.length; i++) {
      if (!obj) return
      obj = obj[segments[i]]
    }
    return obj
  }
}
```
```js
// Observer.js

import Dep from './Dep.js'
  //...
  constructor (value) {
    // 每个 Observer 实例，都有一个属性是 Dep 的实例
    this.dep = new Dep()
  }
```
```js
// array.js

//...
def(arrayMethods, methodName, function () {

    // 最外层传入 observe 的 data 一定是对象，第一次遍历 data 对象时给所有的第一层根属性都调用了 observe，所以数组上一定有 __ob__
    const ob = this.__ob__ // this 就是调用方法的数组
    //...

    ob.dep.notify()

    // ...
}, false)
```
```js
// defineReactive.js

import Dep from './Dep.js'

// ...

// defineReactive 闭包中的 dep，和 Observer 实例中的 dep 不是同一个
const dep = new Dep()

// ...

get() {

  // 如果处于依赖收集阶段
  if (Dep.target) {
    dep.depend()
    if (childOb) {
      childOb.dep.depend()
    }
  }

  return val
},
set(newValue) {
    // 发布订阅模式，触发 set 是通知
    dep.notify()
}

```
```js
new Watcher(obj, 'a.b.c', (val, oval) => {
  console.log(val, 'nval')
  console.log(oval, 'oval')
})
```

# Vue3

待更新...

# Vue2 和 Vue3 响应式区别