---
title: fragments
date: 2019-12-02 11:41:03
tags: 
categories: 计算机常识
---
<center>一些碎片</center>
<!-- more -->

***

## 百度地图控件偏移

```javascript
let map = new BMap.Map('allmapTwo');

let mapType = new BMap.MapTypeControl(
	{
		mapTypes: [BMAP_NORMAL_MAP,BMAP_SATELLITE_MAP],
        // anchor: BMAP_ANCHOR_TOP_LEFT
	}
);
mapType.setAnchor(BMAP_ANCHOR_TOP_LEFT)  //控件位置
mapType.setOffset(new BMap.Size(330, 10))  //控件偏移


map.addControl(mapType1);
```