---
title: 单行文字无缝滚动公告-vue组件及小程序
date: 2020-04-22 17:45:18
tags: ['vue', '微信小程序']
categories: 微信小程序
---
<center>文字滚动公告</center>
<!-- more -->

## vue组件
```	js
//marquee.vue
<template>
    <div class="my-outbox">
        <div class="my-inbox" ref='box'>
            <div class="my-list" v-for="(item,index) in sendVal" :key='index'>
                <span class="my-uname">{{item}}</span>
            </div>
            <div class="my-list" v-for="(item,index) in sendVal" :key='(index+1)*100'>
                <span class="my-uname">{{item}}</span>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name:'marquee',
        props:{        
            sendVal:Array
        },
        data() {
            return {}
        }, 
        mounted:function(){
            var that = this;
            var target = this.$refs.box; 
            var initLeft = 0;
            setInterval(function(){
                initLeft ++;
                if(initLeft >= target.offsetWidth / 2 ){
                    initLeft = 0;
                }
                target.style = 'transform: translateX(-'+ initLeft +'px)';                
            },16)
        }
    }
</script>

<style lang="scss" scoped>
    .my-outbox{
        color: #D7BC8D;
        overflow: hidden;
        height: 35px;
        background: #422b02;
        position: relative;
        // width: 50%;
        // margin: 0 auto;
        .my-inbox{
            white-space: nowrap;
            position: absolute;
            font-size: 0;
            .my-list{
                margin-right: 60px;
                display: inline-block;
                font-size: 13px;
                height: 35px;
                line-height: 35px;
                .my-uname{
                    color: #FF8900;
                }
            }
        }
    }
</style>  
```

```js
//父组件

<template>
    <my-marquee :send-val='newItems'></my-marquee>
</template>

<script>
    import myMarquee from './marquee'
    export default{
        components: {
            myMarquee
        },
        data(){
            return(
                newItems: [
                    //若数据过少可能无法占满滚动窗口，可以将数据复制几遍
                    '公告：添加了新功能',
                    '公告：添加了新功能',
                    '公告：添加了新功能',
                    '公告：添加了新功能',
                    '公告：添加了新功能',
                    '公告：添加了新功能',
                    '公告：添加了新功能'
                ],

            )
        }
    }
</script>
```

## 微信小程序

```html
<!-- index.wxml -->

<view class="my-outbox">
    <view class="my-inbox" style="transform: translateX(-{{initLeft}}px)">
      <view wx:for="{{sendVal}}" wx:for-index="index" wx:for-item="item" wx:key="index" class="my-list">
        <text class="my-uname">{{item}}</text>
      </view>
      <view wx:for="{{sendVal}}" wx:for-index="index" wx:for-item="item" wx:key="(index+1)*100" class="my-list">
        <text class="my-uname">{{item}}</text>
      </view>
    </view>
</view>
```
```js
// index.js

data: {
    sendVal: [
      '公告：添加了新功能添加了新功能',
      '公告：添加了新功能添加了新功能',
      '公告：添加了新功能添加了新功能',
      '公告：添加了新功能添加了新功能',
    ],
    initLeft: 0
},
onshow: function(){
    const that = this
    
    let initLeft = that.data.initLeft;
    let offsetWidth = 0

    // 微信小程序获取DOM节点位置信息
    var query = wx.createSelectorQuery()
    query.select('.my-inbox').boundingClientRect()
    query.exec((rect)=>{
      offsetWidth = rect[0].width
    })
    setInterval(function(){
        initLeft ++;
        
        if(initLeft >= offsetWidth/2){
            initLeft = 0;
        }
        that.setData({
          initLeft: initLeft
        })
    },16)
}
```

```css
/* index.wxss */

.my-outbox{
  color: #D7BC8D;
  overflow: hidden;
  height: 35px;
  background: #422b02;
  position: relative;
  margin-bottom: -35px;
}
.my-inbox{
  white-space: nowrap;
  position: absolute;
  font-size: 0;
}
.my-list{
  margin-right: 25px;
  display: inline-block;
  font-size: 13px;
  height: 35px;
  line-height: 35px;
}
.my-uname{
    color: #FF8900;
}
```

[HTMLElement.offsetWidth 一个元素的布局宽度 MDN](https://developer.mozilla.org/zh-CN/docs/Web/API/HTMLElement/offsetWidth)

完