---
title: css之宽度与高度
date: 2018-03-16 01:08:22
tags: [内联元素宽高, 块级元素宽高, 建议行高, 脱离文档流, margin合并]
categories: CSS笔记
---
<center>CSS宽度与高度</center>
<!-- more -->

使用CSS进行布局与定位，就必须了解CSS中的宽度与高度。
在CSS中：
**1.内联元素的宽度由行高决定，宽度由内容、padding、border和margin决定；**
**2.块级元素的宽度自适应父元素，高度由其内部文档流元素的高度总和决定。**
***
##内联元素之宽高
###1.通过JS Bin调试观察
分别给div和span加一个红色、绿色的border，便于观察：
![border.JPG](https://upload-images.jianshu.io/upload_images/11014353-d14019c3a419df46.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
给span加一个100px的margin，可以看到span左右各增加了100px的margin，宽度增加200px；上下没有变化，高度不变：
![margin.JPG](https://upload-images.jianshu.io/upload_images/11014353-eb475cec32b481ec.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
给span增加一个100px的padding，可以看到span宽度增加200px，高度依然没有变化（红框没有变高）：
![padding.JPG](https://upload-images.jianshu.io/upload_images/11014353-ebeab594202bcc36.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
我们注意到两个span之间有一个空格，这是因为代码中两个span之间并不是没有东西的，而是有回车和空格，即下图中选中部分，显示为一个空格：
![space.JPG](https://upload-images.jianshu.io/upload_images/11014353-12ed6231052813f9.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
将span的border加到10px，可以弹道span的宽度增加了20px，行高依然没有改变：
![border2.JPG](https://upload-images.jianshu.io/upload_images/11014353-57e53016f8eed19d.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
给span设置width和height，宽度和高度没有任何变化：
![wh.JPG](https://upload-images.jianshu.io/upload_images/11014353-506d09bd2634fbd3.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
综上，内联元素宽度受padding和margin影响，高度不受其影响；内联元素不接受指定宽高。
内联元素高度由行高决定，宽度由内容、padding、border、margin决定。
###2.字体与行高
我们给到文本的font-size就是行高吗？答案是否定的。这是因为font-size是字体的最高点到最低点的高度，不同的font-size有不同的**建议行高**。字体设计师在设计字体时，会考虑到如果这种字体要写两行，行与行之间要有空隙，于是设计了建议行高。例如，给定`font-size：20px；`，如果这种字体的建议行高为1.2倍，则行高为24px。如果要明确行高为20px，那么应该写上`line-height: 20px;`。
line-height可以确定内联元素高度，line-height的值是多少像素，内联元素所占的高度就是多少。
***
##块级元素之宽高
###1.文档流（Normal Flow）
W3C规范中是没有`docuement flow`这个概念的，只有Normal Flow,即普通流，文档流是多数中文翻译者的翻译方式问题。
文档流中内联元素默认从左到右排列，宽度不够则自动换行；
文档流中块级元素从上到下排列，每个元素占一行。
一个子元素脱离文档流后计算父元素高度不再算它的高度，脱离文档流的方式：
`float：left；`
`position：absolute；`
`position：fixed;`
相对定位`position: relative;`不脱离文档流，不管位置怎样移动，父元素计算高度仍要算上它的高度。
###2.margin合并
给父元素一个border，给子元素一个100px的margin，可以看到子元素的margin仍在父元素内：![son.JPG](https://upload-images.jianshu.io/upload_images/11014353-b22d953a9ca0d153.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
将父元素的border变为outline，发现子元素上下margin与父元素合并：
![dad.JPG](https://upload-images.jianshu.io/upload_images/11014353-9c4b7c851f19f336.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
如果父元素没有border，那么子元素的margin上下方向会与父元素margin合并。如果不想让margin合并，可以给父元素加上`border-top`和`border-bottom`(不一定是border，也可以是别的东西，如padding，只要可以挡着子元素margin就可以)。
*在CSS中，即使只给到`border-top：0.1px；`，依然会出现一个1px的border-top。*
其他阻止margin合并方法：
1.给父元素加上`overflow: hidden;`,一般不用这种方法，所有东西都不能超出，例如要加悬浮层会遇到问题；
![overflow.JPG](https://upload-images.jianshu.io/upload_images/11014353-89849ff5bbc6d05c.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2.内联元素也可以挡住，写个字，一般不用，没必要无故写个字。
![dang.JPG](https://upload-images.jianshu.io/upload_images/11014353-001a418f6f72fd44.JPG?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

所以子元素的margin能否使父元素变高，取决于是否有东西挡着它。
一个div里还有div，这个div的高度就是里面div的**高度+padding+border+(sometimes)margin**。
一个div里有span，把span的行高加起来就是div的高度。
综上，块级元素宽度自适应父元素，高度是由其内部文档流元素的高度总和决定的。