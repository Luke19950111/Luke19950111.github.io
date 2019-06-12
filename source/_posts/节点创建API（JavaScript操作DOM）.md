---
title: 节点创建API（JavaScript操作DOM）
date: 2018-04-07 23:34:04
tags: 节点创建
categories: JavaScript
---
<center>操作一下DOM也还行</center>
<!-- more -->

## 1. createElement
createElement通过传入标签名创建元素。用法如下：
    
    var div = document.createElement("div");

## 2. createTextNode
createTextNode用来创建一个文本节点，用法如下：

    var textNode = document.createTextNode("这是一个textNode");

createTextNode接收一个参数，这个参数就是文本节点中的文本。
## 3. cloneNode
cloneNode用来复制调用方法的节点。它接收一个boolean参数，表示是否复制子元素，用法如下：

    var parent = document.getElementById("parentElement");
    var parent2 = parent.cloneNode(true);// 传入true
    parent2.id = "parent2";
这段代码通过cloneNode复制了一份parent元素，其中cloneNode的参数为true，表示parent的子节点也被复制，如果传入false，则表示只复制了parent节点。

注意：如果被复制的元素有id，则副本也会复制id，由于id的唯一性，复制节点后必须修改其id。
## 4. createDocumentFragment
createDocumentFragment方法用来创建一个DocumentFragment。DocumentFragment表示一种轻量级的文档，它的作用主要是存储临时的节点用来准备添加到文档中，添加大量节点到文档中时会使用到。
## 5. 总结
1. 以上四种API创建的节点都是孤立的节点，需要使用appendChild添加到文档中；
2. cloneNode要注意被复制的节点是否包含子节点以及事件绑定等问题；
3. 使用createDocumentFragment来解决添加大量节点时的性能问题。

原文地址：http://luopq.com/2015/11/30/javascript-dom/