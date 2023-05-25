---
title: Axios封装
date: 2023-05-25 16:59:21
tags: [ axios, 工程化 ]
categories: JavaScript
---

<center>在工程化项目中对 axios 异步请求做一些封装可以方便使用，本文记录一种我习惯的 axios 封装及使用方法。</center>
<!-- more -->

```js
// service.js 创建 axios 实例，添加请求和响应拦截
const service = axios.create({
  baseURL: ''
})
service.interceptors.request.use(
  config => {
    ...
    return config
  },
  err => {
    return Promise.rject(err)
  }
)
service.interceptors.response.use(
  response => {
    return response.data
  },
  err => {
    return Promise.reject(err)
  }
)
```

```js
// useAxios.js 声明不同的 Method 方法
import service from './service'
const requese = (option) => {
  const { url, method, params, data } = option
  const staticParams = {} // 通用参数，如 uid 等
  return service({
    url,
    method,
    params: Object.assign(params || {}, staticParams),
    data: Object.assign(data || {}, staticParams)
  })
}
async function getFn (option) {
  const res = await request({
    method: 'GET',
    ...option
  })
  return res
}
async function postFn (option) {
  const res = await request({
    method: 'POST',
    ...option,
  })
  return res
}
async function deleteFn (option) {
  const res = await request({
    method: 'DELETE',
    ...option
  })
  return res
}
async function putFn (option) {
  const res = await request({
    method: 'PUT',
    ...option
  })
  return res
}

export const useAxios = () => {
  return {
    get: getFn,
    post: postFn,
    delete: deleteFn,
    put: putFn
  }
}
```

```js
// common.js 通用请求 api
import { useAxios } from './useAxios.js'
const request = useAxios()

export const function methodGet (url, params) {
  return request.get({ url, params })
}
export const function methodPost (url, data) {
  return request.post({ url, data })
}
export const function methodDelete (url, data) {
  return request.delete({ url, data })
}
exoprt const function methodPut (url, data) {
  return request.put({ url, data })
}
```

```js
// 在组件中引入需要的请求方法使用
import { methodGet, methodPost } from '@/src/axios/common.js'

methodGet('api/xxx', {}).then(res => {
  console.log(res)
})

methodPost('api/xxx', {}).then(res => {
  console.log(res)
})
```