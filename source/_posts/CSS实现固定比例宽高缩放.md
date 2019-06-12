---
title: CSS实现固定比例宽高缩放
date: 2018-05-10 02:18:26
tags: 固定比例缩放
categories: CSS笔记
---
<center>CSS固定比例缩放</center>
<!-- more -->

**需求**
在一个产品列表中，有若干个item，每个item都左浮，并包含在自适应浏览器宽度的父元素中。**在保持item宽高比例固定的情况下，使item元素可以和父元素同比缩放。**

***

**方法**

```
.item{
    float: left;
    width: 20%;
    height: 0;
    padding-bottom: 40%;   
}
```

上面代码实现了item元素宽高比例固定为1 : 2，并且与其父元素同比缩放。

- 如果一个元素的`height`值是百分比，这个百分比值是相对其**父元素的宽度**而言的，即使对`padding-bottom`和`padding-top`也是如此；
- 计算`overflow`时，元素内容区域（`width`和`height`对应区域）和`padding`区域一起计算，即使`overflow: hidden;`，`padding`区域也会显示；
- 综上，可以用`padding-bottom`代替`height`，将`height`的值设为`0`，使元素的高度等于`padding-bottom`的值。

[参考 Zihua Li](http://zihua.li/2013/12/keep-height-relevant-to-width-using-css/)
