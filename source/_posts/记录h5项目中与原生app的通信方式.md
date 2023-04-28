---
title: 记录h5项目中与原生app的通信方式
date: 2023-04-28 11:38:55
tags: [ h5, hybrid app ]
categories: JavaScript
---

<center>H5 应用中通过调用原生 APP 提供的 Api Bridge 来处理 bug</center>
<!-- more -->

最近在做的 h5 应用主要在原生 APP 的 WebView 中运行，测试中发现以下两个问题在在 web 端难以解决，所以由原生 APP 提供接口处理：
- 安卓系统中，调用相册选择图片时偶现触发 WebView 崩溃，进而导致 App 闪退
- 安卓系统中，拉起键盘时页面没有上移，导致底部输入框被遮挡，无法看到输入内容

## 相册图片选择处理
- 对于图片选择，web 端使用的 vant ui 的 uploader 组件，内部实现即为 `<input type=file>`
- 决定改为调用外部 App 提供的接口，由 App 调用系统原生相册选择图片并返回。这里因为 h5 接入了公司多个不同的项目，针对 App 提供接口的方式主要有两种接入方法（实现相同，都是通过与 App 通信，调用原生获取权限选择图片，再通过回调返回图片数据给 h5）
  - 方法1：
    - App 提供的接口调用函数和 h5 需要回调函数都注入 window 对象里；
    - 在 h5 里调用接口函数，并将回调函数名作为参数传入；
    - App 收到后拉起相册选择图片，将图片二进制流作为参数调用回调函数；
    - h5 等待回调函数被调用后拿到图片数据，生成 url 在页面展示，生成 file 对象作为参数调用 web 后端图片上传接口。
  ```js

  const fn = () => { // 等待外部 App 选择图片后在调用回调函数，放在 Promise 中
    return new Promise((resolve) => {
      window._uploadCallback = (data) => { // 外部 App 调用回调函数时将图片数据作为参数传入
        resolve(data)
      }
      window?.cocos?.someFunctionProvideByApp( // 该方法由 App 提供，用来拉起系统原生相册选择图片
        '_uploadCallback'
      )
    })
  }
  const onImageUpload = async () => {
    const b = await fn()  // 触发图片选择后，调用外部 App 提供的接口，并等待返回图片数据
    const array = []
    for (let i in b) {
      array[i] = b[i]
    }
    const buffer = new ArrayBuffer(array.length)
    const buffer2 = new Uint8Array(buffer)
    for (let i = 0; i < array.length; i++) {
      buffer2[i] = array[i]
    }
    const data = new Blob([buffer2])
    const imgUrl = window.URL.createObjectURL(data) // 图片链接，在页面中展示
    const file = new window.File([data], 'name') // file 对象，可作为参数调用 web 后端接口
    const form = new FormData()
    form.append('file', file)
  }
  ...
  <template>
    <div @click="onImageUpload">上传按钮</div>
  </template>
  ```
  - 方法2：公司部分项目使用 flutter 开发，对 h5 接入比较友好，接口封装程度更高一点，直接提供异步接口返回数据
  ```js
  // 1. 引入bridge.js，其中导出 appUploadImg 方法用于调用相册获取图片
  // 2.
  import Bridge from './bridge'
  const imageData = await Bridge.appUploadImg()
  // 获取二进制数据后同方法1相同处理方式
  ...
  ```

## 键盘遮挡输入框处理
- 首先尝试使用 `scrollIntoView` 处理，未生效
- 调用键盘时由外部 App 获取键盘高度，并触发 h5 定义在 window 上的事件，传入键盘高度；h5 拿到键盘高度后调整输入框位置
  - h5 内在输入框 focus 时开始等待返回值，在 blur 时把输入框移回原位
  - 要注意系统键盘可能自带关闭按钮，关闭键盘后 h5 内输入框仍处于 focus 状态，没有恢复位置，这就需要 App 在关闭键盘时通知到 h5
  
```js
window._getKeyboardTimerid = null // 若存在重复调用问题，可加防抖
const getKeyboardHeight = () => {
    return new Promise((resolve) => {
        window._getKeyboardHeight = (data) => { // 与客户端开发约定好的函数名， App 在拉起键盘时调用该回调
            if (window._getKeyboardTimerid) { window.clearTimeout(window._getKeyboardTimerid) }
            window._getKeyboardTimerid = setTimeout(() => {
                window._getKeyboardTimerid = null
                resolve(data)
            }, 200)
        }
    })
}

const onFocus = async() => {
    const h = await getKeyboardHeight() // 等待 App 返回高度
    const dpr = window.devicePixelRatio
    const y = Number(h) / dpr  // App 返回的高度为设备物理像素，需要做转换
    const el = document.getElementById('input-area')
    el.style.transform = `translateY(-${y}px)`
}
const onBlur = () => {
    const el = document.getElementById('input-area')
    el.style.transform = 'translateY(0)'
}
window._onKeyboardClose = () => { // 由 h5 注入到 window，App 在键盘关闭时调用
    onCommentBlur()
}

...
<template>
  <div id="input-area">
    <input @focus="onFocus" @blur="onBlur" />
  </div>
</template>
```