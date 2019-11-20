---
title: WeUI-tab选项卡组件
date: 2019-11-20 16:26:44
tags: ['WeUI', 'tab']
categories: 微信小程序
---
<center>WeUI tab选项卡组件使用代码</center>
<!-- more -->

***

```html
<view class="page">
  <view class="page__bd">
    <view class="weui-tab">
      <view class="weui-navbar">
        <block wx:for="{{tabs}}" wx:key="*this">
          <view id="{{index}}" class="weui-navbar__item {{activeIndex == index ? 'weui-bar__item_on' : ''}}" bindtap="tabClick">
            <view class="weui-navbar__title">{{item}}</view>
          </view>
        </block>
        <view class="weui-navbar__slider" style="left: {{sliderLeft}}px; transform: translateX({{sliderOffset}}px); -webkit-transform: translateX({{sliderOffset}}px);"></view>
      </view>
      <view class="weui-tab__panel">
        <view class="weui-tab__content" hidden="{{activeIndex != 0}}">选项一的内容</view>
        <view class="weui-tab__content" hidden="{{activeIndex != 1}}">选项二的内容</view>
        <view class="weui-tab__content" hidden="{{activeIndex != 2}}">选项三的内容</view>
      </view>
    </view>
  </view>
</view>
```

```javascript
var sliderWidth = 96; // 需要设置slider的宽度，用于计算中间位置
Page({
  data: {
    tabs: ["选项一", "选项二", "选项三"],
    activeIndex: 1,
    sliderOffset: 0,
    sliderLeft: 0
  },
  onLoad: function () {
    var that = this;
    wx.getSystemInfo({
      success: function(res) {
        that.setData({
          sliderLeft: (res.windowWidth / that.data.tabs.length - sliderWidth) / 2,
          sliderOffset: res.windowWidth / that.data.tabs.length * that.data.activeIndex
        });
      }
    });
  },
  tabClick: function (e) {
    this.setData({
      sliderOffset: e.currentTarget.offsetLeft,
      activeIndex: e.currentTarget.id
    });
  }
});
```