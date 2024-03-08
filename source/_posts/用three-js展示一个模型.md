---
title: 用three.js展示一个模型
date: 2024-03-08 16:15:06
tags: [three.js, 3d]
categories: JavaScript
---

{% asset_img old-hotel.png Old Hotel %}
<!-- more -->

分以下几步用 three.js 展示一个3D模型：
- 创建场景
- 创建相机
- 创建渲染器，循环渲染
- 使用加载器导入模型
- 添加灯光
- 添加控制器

## 创建场景

```js
const scene = new THREE.Scene()
```

## 创建相机
three.js 提供了几个不同种类的相机，这里我们使用透视相机，创建相机后给相机设置一个合适的位置，将相机添加到场景中

```js
const camera = new THREE.PerspectiveCamera(45, window.innerWidth / window.innerHeight, 0.1, 10000)
camera.position.set(10, 10, 20)
camera.lookAt(new THREE.Vector3(0, 0, 0))
scene.add(camera)
```

## 创建渲染器，循环渲染

```js
// 创建渲染器
const renderer = new THREE.WebGLRenderer({antialias: true})
renderer.setSize( window.innerWidth, window.innerHeight )
document.body.appendChild( renderer.domElement )

// 循环渲染场景
function animate() {
  requestAnimationFrame( animate )
  renderer.render( scene, camera )
}
animate()
```

## 导入模型
three.js 支持多种格式的3D模型，并且提供了对那个的导入工具。这里我们使用 glTF，对应使用 GLTFLoader 导入。
[sketchfab 可从这里下载模型](https://sketchfab.com/3d-models?features=downloadable&sort_by=-likeCount)
下面代码只是最简单的模型导入，实际使用时可能需要根据模型调整原点位置等。

```js
import { GLTFLoader } from 'three/addons/loaders/GLTFLoader.js'

const loader = new GLTFLoader()
loader.load('./scene.gltf', function ( gltf ) {
  const model = gltf.scene
  scene.add(model)
}, undefined, function ( error ) { 
  console.error( error )
})
```

## 添加灯光
没有灯光的话仍然无法看到场景中的模型，所以需要添加一些灯光。
three.js 提供了环境光（AmbientLight）、平行光（DirectionalLight）、点光源（PointLight）等多种灯光效果，这里我们添加了环境光和平行光。

```js
scene.add(new THREE.AmbientLight(0xFFFFFF))
const directionalLight = new THREE.DirectionalLight( 0xffffff, 10 )
directionalLight.position.set(10, 10, 10).normalize()
scene.add( directionalLight )
```

## 添加控制器
此时已经可以看到场景中的模型。
如果需要旋转拖放控制模型，需要添加合适的控制器，并根据模型和相机位置等调整配置。

```js
import { MapControls } from 'three/addons/controls/MapControls.js'

const controls = new MapControls( camera, renderer.domElement )
controls.enableDamping = true
controls.dampingFactor = 0.30
controls.screenSpacePanning = false
controls.autoRotate = false
controls.enableZoom = true
controls.zoomSpeed = 2.0
controls.minZoom = Infinity
controls.maxZoom = Infinity
controls.enablePan = true
controls.panSpeed = 5.0
controls.mouseButtons = {
  LEFT: THREE.MOUSE.ROTATE,
  MIDDLE: THREE.MOUSE.DOLLY,
	RIGHT: THREE.MOUSE.PAN
}
```

完成以上几个主要步骤就可以简单的在浏览器中用three.js展示一个3D模型了。

[Old Hotel](https://github.com/Luke19950111/three-38)
Demo中模型较大，加载慢，建议本地运行。