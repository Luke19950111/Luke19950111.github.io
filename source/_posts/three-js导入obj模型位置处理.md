---
title: three.js导入obj模型子模型位置为{0,0,0}之处理
date: 2019-09-18 16:01:36
tags: [three.js, obj模型, position]
categories: JavaScript
---
<center>获取每个子模型的坐标</center>
<!-- more -->

***

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
`sphere.position`可以看做是子模型的位置。

[参考](https://blog.csdn.net/yuanben_wuxin/article/details/79274808)