---
title: CSS-position的五个值
date: 2018-04-01 19:33:36
tags: [static, relative, absolute, fixed, sticky]
categories: CSS笔记
---
<center>static, relative, absolute, fixed, sticky</center>
<!-- more -->

## 1. static
- 正常的布局，即元素在文档常规流中当前布局位置。
- 此时`top`,`right`,`bottom`,`left`和`z-index`属性无效。
## 2. relative
- 元素先放置在未添加定位时的位置，再在不影响页面布局的前提下调整元素位置。
- 会在元素未添加定位时的位置留下空白。**不脱离文档流**。
## 3. absolute
- 不为元素预留空间。**脱离文档流**。绝对定位元素相对于最近的非static祖先元素定位（一般让它的父元素为relative），当这样的祖先元素不存在时，则相对于ICB（inital container block，初始包含块）定位。
- 绝对定位元素可以设置margins，且不与其他外边距合并。
## 4. fixed
- 不为元素预留空间。**脱离文档流**。相对于屏幕窗口（viewport）定位。
- 元素的位置在屏幕滚动时不会改变。
- 当元素祖先的`transform`属性非`none`时，相对于该祖先定位。
## 5. sticky（实验API,尽量不要在生产环境中使用）
- 粘性定位是相对定位(relative)和固定定位(fixed)的混合。元素在跨越特定阈值前为相对定位，之后为固定定位。
- 须指定 `top`,`right`, `bottom` 或 `left` 四个阈值其中之一，才可使粘性定位生效。否则其行为与相对定位相同。