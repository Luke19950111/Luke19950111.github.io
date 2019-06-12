---
title: Node.nextSibling+nodeType递归
date: 2018-04-03 02:16:32
tags: [Node.nextSibling, nodeType]
categories: JavaScript
---
<center>在DOM里找下一个元素节点</center>
<!-- more -->
需求：寻找a元素的“弟弟”，即a元素同一级的下一个元素节点。
方法：

    let brother = a.nextSibling;
    while(brother.nodeType !== 1){
        brother = brother.nextSibling
    }

解释：
1.  `Node.nextSibling`返回其父节点的`childNodes`列表中紧跟在其后的节点，如果指定节点是最后一个节点，则返回null。
浏览器会在源代码标签内部有空白符的地方插入一个文本节点到文档中，因此`Node.nextSibling`可能会得到一个文本节点，需要用递归获得下一个元素节点。
2. `nodeType`属性可用来区分不同类型的节点，常用的两个节点类型常量：
`Node.ELEMENT_NODE`，值为1，元素；
`Node.TEXT_NODE`，值为3，文本。