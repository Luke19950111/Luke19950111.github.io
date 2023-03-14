---
title: css元素位移百分比计算
date: 2023-03-14 11:35:28
tags: [translate, absolute]
categories: CSS笔记
---

<center>translate或绝对定位值为百分比时是根据什么元素计算的？</center>

<!-- more -->

## 功能实现

![](Progress-1.png)

业务中需要实现类似上图进度展示，分析需求可知：
- 整体由图标、文本和连接线组成
- 图标和连接线均匀分布
- 进度文本与对应图标水平居中对齐
- 宽度上文本与连接线有重叠

因为文本与连接线宽度有重叠，所以使用 `position: absolute` 将其绝对定位脱离文档流，其余图标和连接线使用 flex 可轻松实现效果，以下为简化代码（vue）。


```html
const list = ref([
  { name: 'Accepted', time: '2023-03-01' },
  { name: 'Approved', time: '2023-03-02' },
  { name: 'Doing' },
  { name: 'Published' }
])
...
<div class="content-and-line" v-for="(item, index) in list" :key="item">
  <div class="content">
    <img src="link">
    <div class="text">
      <div class="name">{{ item.name }}</div>
      <div class="time">{{ item.time }}</div>
    </div>
  </div>
  <div class="line" v-if="index < list.length - 1"></div>
</div>
```
```scss
<style lang="scss" scoped>
@mixin center() {
  display: flex;
  justify-content: center;
  align-items: center;
}
.content-and-line {
  @include center;
  .content {
    text-align: center;
    position: relative;
    img {
      width: 53px;
      height: 53px;
    }
    .text {
      position: absolute;
      // left: 50%;
      // transform: translateX(-50%);
    }
  }
  .progress-line {
    width: 85px;
    border-top: 2px solid #2C2C2C;
    margin: -1px 20px 0;
  }
}
</style>
```

实现效果如图：
![](Progress-2.png)

还需要移动文本位置与图标居中对齐。

首先想到将文本向左移动自身的一半： `transform: translateX(-50%);`
这里需要知道，**transform(x, y)，x、y值为百分比时会以自身的宽高作为参考**

![](Progress-3.png)

此时文本中心与图标左侧对齐，需要再向右移动图标宽度的一半；
content中有img和text两个子元素，因为text已经脱离文档流，此时父元素content的实际宽度就是img的宽度。
也就是文本需要再向右移动父元素宽度的一半。
**css设置绝对定位后 top,bottom,设置百分比定位是按父元素的高度来计算的，同样left,right,设置百分比定位是按父元素的宽度来计算的，**
文本已经绝对定位，只需要使用 `left: 50%;`即可实现。

## 总结

- transform(x, y)，x、y值为百分比时会以自身的宽高作为参考
- 绝对定位后，left、right百分比定位按父元素宽度计算，top、bottom百分比定位按父元素高度计算。


