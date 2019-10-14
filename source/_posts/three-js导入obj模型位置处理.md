---
title: three.js导入obj模型子模型位置为{0,0,0}之处理
date: 2019-09-18 16:01:36
tags: [three.js, obj模型, position]
categories: JavaScript
---
<center>获取每个子模型的坐标</center>
<!-- more -->

***
## 获得子模型坐标
代码：
```javascript
//子模型为 obj

obj.geometry.computeBoundingBox();
 
var centroid = new THREE.Vector3();
centroid.addVectors( obj.geometry.boundingBox.min, obj.geometry.boundingBox.max );
centroid.multiplyScalar( 0.5 );
 
centroid.applyMatrix4( obj.matrixWorld );

let sphere = new THREE.Mesh(new THREE.SphereGeometry(1, 1, 1),
  new THREE.MeshBasicMaterial({
    color: 0xffffff,
    wireframe: true
  })
);
sphere.position.set(centroid.x, centroid.y, centroid.z)
```

通过获取obj子模型最小值和最大值来计算其质心位置,在其质心位置画个球。
子模型 obj 的坐标可以看做是 `centroid.x, centroid.y, centroid.z`

[参考](https://blog.csdn.net/yuanben_wuxin/article/details/79274808)

## 改变控制中心

当场景中只显示一个子模型时，可以把子模型的坐标作为中心点。
把相机视角指向子模型：
```javascript
camera.lookAt(new THREE.Vector3(centroid.x, centroid.y, centroid.z))
```
结果视角并没有变化。原因是受到 `OrbitControls` 控件的影响
改变`OrbitControls.target`即可：
```javascript
controls.target = new THREE.Vector3(centroid.x, centroid.y, centroid.z);
```
再调整相机位置，使子模型显示在场景中间：
```javascript
// 直接移动相机位置
camera.position.set(centroid.x, centroid.y+200, centroid.z+200)

// 用 TweenMax 动画库
TweenMax.to(camera.position, 1, {
  x: centroid.x,
  y: centroid.y+200,
  z: centroid.z+200,
  ease: Expo.easeInOut,
  onComplete: function () {}
})
```
恢复显示整个模型时，只需`controls.reset()`，即可将相机位置及视角还原。

[参考](https://blog.csdn.net/unirrrrr/article/details/80692267)