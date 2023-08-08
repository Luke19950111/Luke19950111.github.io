---
layout: webview
title: webview 中页面内存优化
date: 2023-08-08 17:09:17
tags: ['webview', '内存优化']
categories: 计算机常识
---

<center>H5 页面运行在原生应用的 webview 中，占用内存达到一个阈值会导致 webview 崩溃。</center>
<!-- more -->

## webview 是由系统提供的

- ios webview 是 ios 中内置的浏览器控件，ios 系统从版本9开始使用 `WKWebView` 替换了旧的 UIWebView。
- 安卓使用谷歌提供的 `Android System WebView`，可以在设置-应用中查看 webview 版本号等信息。
- 在不同的原生 app 中，所使用的 webview 都是由系统提供的，而不是 app 提供的。


## webview 占用系统内存的组成

webview总内存=native内存+gpu内存

native内存包括：v8、BlinkGC 、PartitionAlloc、DiscardableSharedMem、SharedMemory、malloc、fontcache、web_cache。
- v8：v8 Javascript 引擎所管理，一般用于分配 Javascript 对象和数据。
- BlinkGC：是标记式垃圾回收算法堆，一般用于管理页面运行上下文对象。
- PartitionAlloc：为分桶式内存分配算法，用于解析、排版、页面运行上下文以及临时内存。
- malloc：用于在运行时动态分配内存空间，类 libc 堆实现的仅用于浏览器内核渲染引擎分配算法，缓存、页面运行上下文、临时内存都有用到。
- SharedMemory： 为多进程共享内存。
- DiscardableSharedMem ：一般为图片解码缓存和 GL 资源缓存等所用。

gpu内存包括：
- gpu、skia。

## 优化方案参考
1. 降低 gpu 内存占用
     - 一定在屏幕内的多个图片，不会通过滑动而影响显示的，可以试一下合成一张图片，减少图片内空白区域的占用，达到降低内存的目的
     - 超出屏幕之外的任何元素，不要保留任何引用，进行删除操作，或者使用虚拟滚动
     - 避免内存泄露
     - 尽可能的减少标签创建的数量来达成同样绘制的目的
     - 图片压缩，多图片地方尽量使用缩略图
     - 避免使用非常大的 canvas 以达到"高清的目的"
     - 禁止使用 canvas 的 webgl 模式
     - 设计角度上减少背景图的使用，尽量以 css 绘制简单元素为主
     - 流程上尽量采取进入新页，释放旧页的方式
     - 任意图片的懒加载也是有效降低瞬时内存的方式
     - 对于 css 动画，应该减少动画的时长，帧率，复杂性，同时使用动画加速。
2. 降低 js 代码的内存占用
    - 降低虚拟 dom 的缓存量，切换路由尽量不对虚拟 dom 进行缓存，尽量从业务设计上解决这个问题
    - js 堆内存的申请导致内存泄露问题，减少大的全局变量的使用，尽量在函数中，同时也要注意闭包带来的内存泄露问题
    - 禁止使用 web worker 多进程操作
3. webview层优化（需要原生应用实现）
    - webView reload
    - 避免使用多进程