---
title: vue-router：过渡动画
date: 2019-06-19 14:40:35
tags: [vue-router, 过渡动画]
categories:
- vue
- vue-router
---
<center>过渡动画</center>
<!-- more --><br/>

# 代码示例

- 在`<router-view>`标签外部添加`<transition>`标签，标签还需要一个`name`属性：

```
<transition name="fade" mode="out-in">
    <router-view />
</transition>
```

- 加入CSS，一共6个CSS类名：`v-enter`，`v-enter-active`，`v-enter-to`，`v-leave`，`v-leave-active`，`v-leave-to`，类名与transition的name属性有关，比如name=”fade”，相应的css如下：

```
/*页面切换动画*/
/*进入过渡的结束状态，元素被插入时就生效，在过渡过程完成后移除*/
.fade-enter-active {
    transition: opacity .5s;
}
/*进入过渡的开始状态，元素被插入时生效，只应用一帧后立刻删除*/
.fade-enter {
    opacity: 0;
}
/*离开过渡的开始状态，元素被删除时触发，只应用一帧后立刻删除*/
.fade-leave {
    opacity: 1;
}
/*离开过渡的结束状态，元素被删除时生效，离开过渡完成后被删除*/
.fade-leave-active {
    opacity:0;
    transition: opacity .5s;
}
```

看图理解
[文档 过渡的类名](https://cn.vuejs.org/v2/guide/transitions.html#%E8%BF%87%E6%B8%A1%E7%9A%84%E7%B1%BB%E5%90%8D)

# 过渡模式mode

- `in-out`：新元素先进入过渡，完成之后当前元素过渡离开，默认模式。
- `out-in`：当前元素先进行过渡离开，离开完成后新元素过渡进入。

[参考教程](http://doc.liangxinghua.com/vue-family/1.html)
