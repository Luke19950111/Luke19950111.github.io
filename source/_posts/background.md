---
title: background
date: 2019-06-14 15:01:54
tags: css
categories:
- CSS笔记
- css属性
---
<center>The background can be styled with different colors, images (with repeat or no repeat properties of images).</center>
<!-- more -->

## background-color

```
<style>
body{
 background-color: lightblue;
}
h1{
 background-color:gray;
}
p{
 background-color:yellow;
}
</style>
```

## background-image

```
<style>
body
{
 background-image:url("/images/codingb-96x96.png");
}
</style>
```

## background-repeat

```
<style>
body
{
 background-repeat:no-repeat; //It does not allow the image to repeat
}
</style>
Try </>
```

```
{background-repeat:repeat-x;}
{background-repeat:repeat-y;}
{background-repeat:space;}
{background-repeat:round;}
```

## background-attachment

```
{background-attachment:fixed;}
{background-attachment:scroll;}
```

## background-position

```
{background-position:right;}
{background-position:left;}
{background-position:top;}
{background-position:bottom;}
{background-position:center;}
```

## background-clip

```
{background-clip:padding-box;}
{background-clip:content-box;}
```

## background-size

```
{background-size:cover;}
{background-size:contain;}
{background-size:200px;}
```

[详情](https://www.web4college.com/css/css_background.php)