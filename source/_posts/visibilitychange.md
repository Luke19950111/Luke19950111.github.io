---
title: visibilitychange
date: 2018-04-23 23:42:41
tags: [visibilitychange, 自动播放轮播]
categories: JavaScript
---
<center>关爱浏览器，从我做起</center>
<!-- more -->

我制作了一个自动播放的无缝轮播：
[自动播放](https://luke19950111.github.io/sliders-demo-1/index.html)
[代码](https://github.com/Luke19950111/sliders-demo-1)
现在我发现它有一个bug：每次切换到其他标签的网页，再切换回来时，播放出现了混乱。

我尝试写如下代码：
```
setInterval(function(){
  console.log(new Date())
},500)
```
也就是每秒打印两次当前事件。
它确实在执行，[每秒打印两次](https://jsbin.com/buzilisowi/edit?js,output)。(请在控制台中查看)
但是，当我切换到其他页面再切回来时，发现在我离开页面的时候它每秒只会打印一次（可点击以上链接尝试），浏览器在我的视线离开它时偷懒了。
这是因为浏览器要节省性能，当发现用户不看当前页面时，setInterval的频率变慢了。
同理，我的自动轮播也是每秒切换一次状态( [代码](https://github.com/Luke19950111/sliders-demo-1/blob/master/main.js) )，当我切花到其他页面时，频率变慢，切回来时恢复，导致播放混乱。

要避免这个bug，干脆在我切出页面的时候让自动轮播停下来，也就是只有用户观看轮播的时候，才会自动展示下一张幻灯片。
这里我学到了 [Page Visibility API](https://developer.mozilla.org/zh-CN/docs/Web/API/Page_Visibility_API)。
>当一个网页是可见或点击选中的状态时 Page Visibility API 可以让你获取到这种状态。在用户使用切换标签的方式来浏览网页时,非常合理的情况是任何在后台页面都不会展示给用户。 当用户最小化网页或者浏览到其他标签的网页时，API将发送一个关于当前页面的可见信息的事件`visibilitychange` 。你可以检测该事件然后执行一些活动或是展示不同的效果。——MDN

好处：节约资源。
`document.hidden`：页面隐藏时返回true，否则返回false；

代码改为
```
document.addEventListener('visibilitychange',function(){
    if(document.hidden){
        //停止播放
    }else{
        //恢复播放
    }
})
```
