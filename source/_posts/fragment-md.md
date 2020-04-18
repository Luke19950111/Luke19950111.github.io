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

## vue 箭头函数使用注意
不要在选项属性或回调上使用箭头函数，例如：
```javascript
created: () => console.log(this.a)
```
或
```javascript
vm.$watch('a', newValue => this.myMethod())
```
因为箭头函数并没有 `this`，`this` 会作为变量一直向上级词法作用域查找，直至找到为止。
[vue文档](https://cn.vuejs.org/v2/guide/instance.html#%E5%AE%9E%E4%BE%8B%E7%94%9F%E5%91%BD%E5%91%A8%E6%9C%9F%E9%92%A9%E5%AD%90)


## vue 模板语法插值使用javascript表达式
vue中所有的数据绑定都支持javascript表达式：
```javascript
{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}

<div v-bind:id="'list-' + id"></div>
```
每个绑定都只能包含单个表达式，所以下面的例子都**不会**生效：
```
<!-- 这是语句，不是表达式 -->
{{ var a = 1 }}

<!-- 流控制也不会生效，请使用三元表达式 -->
{{ if (ok) { return message } }}
```
[vue文档](https://cn.vuejs.org/v2/guide/syntax.html#%E4%BD%BF%E7%94%A8-JavaScript-%E8%A1%A8%E8%BE%BE%E5%BC%8F)

## JS中的逻辑运算符 &&、||，位运算符 |， &
- 在js逻辑运算中， `0`、`''`、`null`、`undefined`、`NaN`、`false`会判断为false，其他都是true。
- `||`
只要 `||` 前面为false，不管 `||` 后面是true还是false，都返回 `||` 后面的值；
只要 `||` 前面为true，不管 `||` 后面是true还是false，都返回 `||` 前面的值。

- `&&`
只要 `&&` 前面是false，不管 `&&`后面是true还是false，都返回 `&&` 前面的值；
只要 `&&` 前面是true，不管 `&&` 后面是true还是false，都返回 `&&` 后面的值。

- `|`
转为二进制运算，两个位只要有一个为1，结果为1，否则为0
例如：3|4 3的二进制为 `011`，4的二进制为 `100`， `011 | 100` 结果为 `111` ，即7

- `&`
转为二进制运算，两个位同时为1得1，只要一个为0得0
例如：3&4 3的二进制为 `011`，4的二进制为 `100`， `011 &100` 结果为 `000` ，即0