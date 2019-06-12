---
title: box-sizing -css
date: 2018-03-13 12:37:30
tags: box-sizing
categories: CSS笔记
---
<center>box-sizing盒子模型</center>
<!-- more -->
box-sizing属性用于更改用于计算元素宽度和高度的默认的CSS盒子模型。

**关键字 值**<br>
box-sizing: content-box;
box-sizing: border-box;

**全局 值**<br>
box-sizing: inherit;
box-sizing: initial;
box-sizing: unset;

**content-box**<br>
默认值，标准盒子模型。width和height只包括内容区的宽和高，不包括padding、border和margin。<br>
如 .box{width: 500px;} {border:10px solid black} 那么在浏览器中渲染的实际宽度是520px。

**border-box**<br>
width和height属性包括内容、padding和border。<br>
内容区实际宽度是width减去border+padding的计算值。
