---
title: animation
date: 2019-06-14 16:49:09
tags: [css, animation]
categories: 
- CSS笔记
- css属性
---
<center>Animations change the CSS properties of an element during the sub-intervals of a specific interval of time. CSS properties can be overriden by the new properties in the next upcoming sub-intervals.</center>
<!-- more -->

## animation-name property

```
<style>
div{
 background-color:red;
 animation-name:changeBG;
}
@keyframes changeBG{
from{
 background-color:red;
}
to{
 background-color:green;
}	
}
</style>
```

## animation-time property

```
<style>
div{
 background-color:red;
 animation-name:changeBG;
 animation-duration:2s;
}
@keyframes changeBG{
0%{
 background-color:red;
}
50%{
 background-color:green;
}
100%{
 background-color:blue;
}	
}
</style>
```

## animation-iteration-count property

```
<style>
div{
 position:relative;
 animation-name:changeBG;
 animation-iteration-count:5; //重复次数
}
@keyframes changeBG{
0%{
 left:0%;
}
100%{
 left:100%;
}	
}
</style>
```

## animation-direction

`normal`, `reverse`, `alternate` or `alternate-reverse`

```
<style>
div{
 position:relative;
 animation-name:changeBG;
 animation-direction:reverse;
}
@keyframes changeBG{
0%{
 left:0%;
 top:0%
}
100%{
 left:80%;
 top:0%;
}	
}
</style>
```

## animation-delay

It sets the offset time before starting the animation.

```
<style>
div{
 position:relative;
 animation-name:changeBG;
 animation-delay:2s;
}
@keyframes changeBG{
0%{
 left:0%;
 top:0%
}
100%{
 left:80%;
 top:0%
}
}
</style>
Try </>
```

## keyframe rule (@keyframes)

keyframe rule consists of **@keyframes** keyword followed by the animation-name and percentage values of time. The percentage values represent the intervals of time. The time is obtained by the value of **animation-duration**.

```
<style>
div{
 background-color:red;
 animation-name:changeBG;
 animation-duration:2s;
}
@keyframes changeBG{
0%{
 background-color:red;
}
50%{
 background-color:green;
}
100%{
 background-color:blue;
}	
}
</style>
```

animation-time defines the time during which this animation will occur.

0% means 0s. 
50% means 50% of animation-duration i.e. 2s.
100% means 100% of animation-duration i.e. 4s.

[详情](https://www.web4college.com/css/web-css-animation.php)
