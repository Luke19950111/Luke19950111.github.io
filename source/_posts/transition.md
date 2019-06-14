---
title: transition
date: 2019-06-14 17:29:33
tags: [css, transition]
categories:
- CSS笔记
- css属性
---
<center>CSS transition changes (updates) the value of some CSS property from old to a new value within specific interval of time. This property occurs as a result of some event. This event may be onhover, onclick or an animation property.</center>
<!-- more -->

## transition-property

```
<style>
div{
  transition-property:color,font-size,background-color;
  color:red;
  background-color:white;
  font-size:16px;
}
div:hover{
  color:white;
  background-color:red;
  font-size:20px;
}
</style>
```

## transition-duration

```
<style>
div{
  transition-property:background-color;
  transition-duration:2s;
  background-color:red;
}
div:hover{
  background-color:blue;
}
</style>
```

If more than values have been specified for the transition-duration, then corressponding values are specified in the same order.

```
<style>
div{
  transition-property:color,background-color;
  transition-duration:1s,2s;
  color:red;
  background-color:white;
}
div:hover{
  color:white;
  background-color:red;
}
</style>
```

The example shows that the color and background-color change with 1s and 2s respectively.

## transition-timing-fuction

There are four values of transition-timing-fuction such as `ease`, `ease-in`, `ease-out` and `ease-in-out`.

```
<style>
div{
  position:relative;
  transition-property:top;
  top:0px;
  transition-duration:2s;
  transition-timing-function:ease-in;
}
div:hover{
  top:100px;
}
</style>
```

## transition-delay

```
<style>
div{
  position:relative;
  transition-property:font-size;
  font-size:16px;
  transition-duration:1s;
  transition-delay:2s;
}
div:hover{
  font-size:20px;
}
</style>
```

[详情](https://www.web4college.com/css/web-css-transition.php)
[Transition MDN](https://developer.mozilla.org/zh-CN/docs/Web/CSS/transition)

## transition 和 animation 区别

- transition 不能自行触发，通过hover等动作，或者结合js进行触发；animation可以自行运行
- transition可控性相对较弱，只能够指定起始和结束的状态，而animation可以定义多个关键帧
- animation通过模拟属性值改变来实现动画，动画结束之后元素的属性没有变化；而Transition确实改变了元素的属性值，动画结束之后元素的属性发生了变化
- 动画在运行结束之后，需要回到初始状态
- Transform和width、left一样，定义了元素很多静态样式，只不过通过Transition和Animation指定如何改变不同的属性值，才实现了动画