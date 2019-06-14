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

## animation-timing-function

[animation-timing-function MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/animation-timing-function)

```
animation-timing-function: ease;
animation-timing-function: ease-in;
animation-timing-function: ease-out;
animation-timing-function: ease-in-out;
animation-timing-function: linear;
animation-timing-function: step-start;
animation-timing-function: step-end;
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
```

## animation-play-state

```
animation-play-state: paused; //规定动画已暂停
animation-play-state: running; //规定动画正在播放
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
[Animations MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/CSS_Animations)

## transition 和 animation 区别

- transition 不能自行触发，通过hover等动作，或者结合js进行触发；animation可以自行运行
- transition可控性相对较弱，只能够指定起始和结束的状态，而animation可以定义多个关键帧
- animation通过模拟属性值改变来实现动画，动画结束之后元素的属性没有变化；而Transition确实改变了元素的属性值，动画结束之后元素的属性发生了变化
- 动画在运行结束之后，需要回到初始状态
- Transform和width、left一样，定义了元素很多静态样式，只不过通过Transition和Animation指定如何改变不同的属性值，才实现了动画