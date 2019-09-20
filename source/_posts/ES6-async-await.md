---
title: async&await
date: 2019-06-27 10:26:23
tags: [ES6, async, await, promise]
categories: 
- JavaScript
- ES6
---
<center>ECMAScript 2017 ES8</center>
<!-- more --><br/>

- `await`关键字只能放在有`async`前缀的函数里用

- `await`后面接一个会`return new Promise`的函数，并调用这个函数 （async，await与promise结合使用）

- 使用`async`和`await`，如果代码里使用了异步函数，它会看起来更像标准的同步函数。也就是将异步写成同步的样式。 （async，await使异步函数看起来更像是同步函数）

# Promise

下面代码示例为摇一个骰子

```
function fn(){
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      let n = parseInt(Math.random() * 6 + 1,10) // 1-6
      resolve(n) //成功时返回n
    }, 3000)
  })
}

fn().then(
  (x) => {console.log('点数是' + x)}, //成功时调用第一个
  () => {} //失败时调用第二个
)
```

## promise.all()

```
Promise.all([fn1(), fn2()]) //接收一个数组，数组里的每个函数都返回一个promise
    .then(
        () => {}, //所有函数都执行成功时调用
        () => {}  //任何一个函数执行失败时调用
    )
```

## promise.race()

```
Promise.all([fn1(), fn2()]) //接收一个数组，数组里的每个函数都返回一个promise
    .then(
        () => {}, //只要有任何一个函数执行成功，就算成功
        () => {}  //所有函数都执行失败时调用
    )
```

# async,await

```
function fn(){
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      let n = parseInt(Math.random() * 6 + 1,10) // 1-6
      resolve(n) //成功时返回n
    }, 3000)
  })
}

async function test(){
  let n = await fn()  
  //fn先执行，3s后执行完才会赋值给n，也就是函数test要等3s才能执行，函数test是一个异步函数，所以要加async标记
  //await关键字只能放在有async前缀的函数里用
  //await后面接一个会 return new Promise 的函数，并调用这个函数
  console.log(n)
}

test()
```

## reject用法

下面代码示例为投骰子猜大小

```
function 猜大小(x){
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      let n = parseInt(Math.random() * 6 + 1,10) // 1-6
      if(n>3){
        if(x === '大'){
          resolve(n)
        }else{
          reject(n)  //如果猜错了，抛出n
        }
      }else{
        if(x === '小'){
          resolve(n)
        }else{
          reject(n)  //如果猜错了，抛出n
        }
      }
    }, 3000)
  })
}

async function test(){
  try{
    let n = await 猜大小('大')
    console.log(n, '猜对了')
  }catch(err){
    console.log(err, '猜错了')
  }
  
}

test()
```

为什么用`await`、`async`而不用promise的`.then`？

为了让异步代码看起来更像同步代码。这就是发明这个语法的初衷。

## 同时猜两个  

用`promise`写：

```
function 猜大小(x){
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log('开始')
      let n = parseInt(Math.random() * 6 + 1,10) // 1-6
      if(n>3){
        if(x === '大'){
          resolve(n)
        }else{
          console.log('猜小出大')
          reject(n)
        }
      }else{
        if(x === '小'){
          resolve(n)
        }else{
          console.log('猜大出小')
          reject(n)
        }
      }
    }, 3000)
  })
}

Promise.all([猜大小('大'), 猜大小('小')])
  .then(
    (x) => {console.log(x,'都猜对了')},
    (y) => {console.log(y,'没全猜对')}
  )

```

用`async`，`await`写：（还是要用到promise）

```
async function test(){
  try{
    let n = await Promise.all([猜大小('大'),猜大小('小')])
    console.log(n, '都猜对了')
  }catch(err){
    console.log(err, '没全猜对')
  }
}

test()
```