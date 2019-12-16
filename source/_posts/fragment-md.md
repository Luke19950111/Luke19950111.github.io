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

## element `previewSrcList` 开启预览大图的功能

```html
<el-image-viewer
    v-if="showViewer"
    style="width: 100%;height:600px;position: relative;"
    :on-close="closeViewer"
    :url-list="srcList" />


<el-button @click="onPreview">查看图片</el-button>

```

```html
<script>
    import ElImageViewer from 'element-ui/packages/image/src/image-viewer'
    export default{
        name: 'preview',
        data(){
            return {
                showViewer: false,
                srcList: [
                    'url1',
                    'url2',
                    'url3'
                ]
            }
        },
        components: {
            ElImageViewer
        },
        methods: {
            onPreview() {
             this.showViewer = true
            },
            closeViewer() {
             this.showViewer = false
            },
        }

    }
</script>

```

## element Popover使用
```html
<img src="按钮图标.png" v-popover:popover1>
<el-popover
    placement="bottom-start"
    ref="popover1"
    width="225"
    trigger="hover">

    <!-- img要放在div里 -->
    <div><img src="弹框里的图片.jpg" slot="reference"></div>
</el-popover>
```