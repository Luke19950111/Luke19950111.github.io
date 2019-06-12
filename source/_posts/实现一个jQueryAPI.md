---
title: 实现一个jQueryAPI
date: 2018-04-12 01:29:26
tags: [jQuery, API]
categories: JavaScript
---
<center>jQuery原来是这么回事</center>
<!-- more -->

## 通过以下四步实现jQuery：
1. 接受一个node或选择器
2. 封装成一个伪数组
3. 在这个伪数组上加上几个API
4. 把这个伪数组送出去

## 本文通过这四步实现两个jQuery API：

    $div.addClass('red') // 可将所有div的class添加一个red
    $div.setText('hi') // 可将所有div的textContent变为hi
    
## 实现过程
根据以上四步，代码结构为：

    ```
    window.jQuery = function(nodeOrSelector){
        let nodes = {0: nodeOrSelector, length: 1}
        
        node.addClass = function(classes){}
        node.text = function(text){}
        
        return nodes
    }
    ```

jQuery拿到一个参数后，首先分析它是节点还是选择器：
如果是选择器（字符串），找到对应的元素，放到一个伪数组里；
如果是节点，为了**保证返回结果一致**，也要放到一个伪数组里：
    
    ```
    let nodes
    if(typeof nodeOrSelector === 'string'){
        nodes = document.querySelectorAll(nodeOrSelector) //querySlelctorAll返回伪数组
    }else if(nodeOrSelector){
        nodes = {0: nodeOrSelector, length: 1} //返回伪数组，保证返回结果一致
    }
    ```

要使选择器返回的伪数组是一个**纯净的伪数组**（原型链直指object.prorotype,中间不再引入其他原型),可以使用一个临时变量存querySelectorAll返回的伪数组，再从中取出需要的值：
    
    ```
    if(typeof nodeOrSelector === 'string'){
        let temp = document.querySelectorAll(nodeOrSelector);
        for(let i=0; i<temp.length; i++){
            nodes[i] = temp[i]; //需要初始化一个伪数组,将上方nodes声明改为let nodes = {}
        }
        nodes.length = temp.length
    }
    ```
    
此时我们已经接受了一个node或选择器，并将其封装成了一个伪数组，接下来我们在这个伪数组上加两个API：
.addClass

    ```
    nodes.addClass = function(oneClass){
        for(let i=0; i<nodes.length; i++){
            nodes[i].classList.add(oneClass)
        }
    }
    ```

.setText

    ```
    nodes.setText = function(text){
        for(let i=0; i<nodes.length; i++){
            nodes[i].textContent = text;
        }
    }
    ```
    
return伪数组nodes，最后给个缩写，实现两个jQuery API：

    ```
    window.$ = jQuery
    var $div = $('div')

    $div.addClass('red') // 可将所有div的class添加一个red
    $div.setText('hi') // 可将所有div的textContent变为hi
    ```
    
完整代码：https://github.com/Luke19950111/acquirejQueryAPI
