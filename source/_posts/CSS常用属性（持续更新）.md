---
title: CSS常用属性（持续更新）
date: 2018-04-01 18:47:11
tags: css属性
categories: CSS笔记
---
<center>CSS属性</center>
<!-- more -->

本文记录我在学习中遇到的一些css属性，持续更新。
## 1. opacity
opacity属性指定了一个元素的透明度，也就是指定一个元素后面的背景被覆盖程度。
- opacity：1；元素完全不透明（即元素后面背景不可见）
- opacity: 0.6;(0.0~1.0之间） 元素半透明（即元素后面的背景可见）
- opacity: 0; 元素完全透明（即元素不可见)

## 2. z-index
z-index元素指定了一个具有定位属性的元素及其子代元素的z-order。
当元素之间重叠的时候，z-order决定哪一个元素覆盖在其余元素上方显示。
通常来说z-index较大的一个会覆盖较小的一个。
元素可拥有负的z-index值。
可能的值：
- auto 默认，堆叠顺序与父元素相等
- number 设置元素堆叠顺序
- inherit 规定应该从父元素继承z-index的值

## 3. transform
transform属性允许你修改CSS视觉格式模型的坐标空间。使用它，元素可以被转换（translate）、旋转（rotate）、缩放（scale）、倾斜（skew）。
transform属性只对block元素生效。

## 4. cursor
cursor属性定义鼠标指针悬浮在元素上方显示的鼠标光标。

|值|描述|
|:-:|:-:|
|auto|初始值。浏览器根据目前内容决定指针样式|
|pointer|悬浮于链接上时，通常是手|
|text|指示文字可被选中|
|none|无指针被渲染|

[cursor MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/cursor)

## 5. text-align
`text-align`定义**行内内容**如何相对于它的**块父元素**对齐。
`text-align`并不控制块元素自己的对齐，只控制它的行内内容的对齐。

|值|描述|
|:-:|:-:|
|left|行内内容向左侧边对齐|
|right|行内内容向右侧边对齐|
|center|行内内容居中|
|justify|文字向两侧对齐，但是对最后一行无效|
|justify-all|和justify一致，但是强制使最后一行两端对齐|

[text-align MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/text-align)

## 6. background-size
`background-size`设置背景图片大小。

|值|描述|
|:-:|:-:|
|cover|缩放背景图片以完全覆盖背景区，可能背景图片部分看不见|
|contain|缩放背景图片以完全装入背景区，可能背景区部分空白|
|auto|以背景图片的比例缩放背景图片|
|一个值|这个值指定图片的宽度，图片的高度隐式的为auto|
|两个值|第一个值指定图片的宽度，第二个值指定图片的高度|
|逗号分隔多个值|设置多重背景|

没有被背景图片覆盖的区域会用`background-color`属性设置背景颜色。

[background-size MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/background-size)


