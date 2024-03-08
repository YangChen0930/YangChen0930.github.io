---
title: cesium加载地形图层
date: 2022-03-08 16:56:18
tags:
    - webgl
    - cesium
---

## 1.创建地形图层

| **接口**                     | **说明**                                                     |
| ---------------------------- | ------------------------------------------------------------ |
| **createWorldTerrain**       | 提供了全球在线地形数据                                       |
| **EllipsoidTerrainProvider** | 它提供了一个全球范围内高度为0的地形，不需要额外的地形文件，就可以实时的自己来构建这个高度为0的Mesh |
| **CesiumTerrainProvider**    | 以cesium的格式访问地形数据                                   |
| **sampleTerrain**            | 允许我们使用异步方式请求指定坐标点的高程信息                 |

<!-- more -->

基本创建方式

```javascript
viewer = new Cesium.Viewer("vueCesium", {
  baseLayerPicker: false, // 如果设置为false，将不会创建右上角图层按钮。
  geocoder: false, // 如果设置为false，将不会创建右上角查询(放大镜)按钮。
  navigationHelpButton: false, // 如果设置为false，则不会创建右上角帮助(问号)按钮。
  homeButton: false, // 如果设置为false，将不会创建右上角主页(房子)按钮。
  sceneModePicker: false, // 如果设置为false，将不会创建右上角投影方式控件(显示二三维切换按钮)。
  animation: false, // 如果设置为false，将不会创建左下角动画小部件。
  timeline: false, // 如果设置为false，则不会创建正下方时间轴小部件。
  fullscreenButton: false, // 如果设置为false，将不会创建右下角全屏按钮。
  selectionIndicator: false,
  terrainProvider: Cesium.createWorldTerrain(),
  imageryProvider:  new Cesium.UrlTemplateImageryProvider({
      url: "https://webrd03.is.autonavi.com/appmaptile?lang=zh_cn&size=1&scale=1&style=7&x={x}&y={y}&z={z}",
    })
});
```

在viewer初始化时将baseLayerPicker设置为true，在控件的最下方就是地形切换按钮

![null](https://s2.loli.net/2024/01/08/wk6pCGsIVmdqJZQ.png)
左边对应是EllipsoidTerrainProvider，右边对应是createWorldTerrain。

## 2.2离线地形

加载离线地形图层和影像图层的操作时一致的。使用CesiumTerrainProvider接口

```javascript
var terrainProvider = newCesium.CesiumTerrainProvider({
    url: 'xxxxxx'
});
```



## 2.3地形数据采样

这个需要我们用到sampleTerra接口，根据文档我们可知，sampleTerrain返回是promise异步类型对象，需要使用when接口调用获取结果。

```javascript
var terrainProvider = Cesium.createWorldTerrain();
var positions = [
    Cesium.Cartographic.fromDegrees(86.925145, 27.988257),
    Cesium.Cartographic.fromDegrees(87.0, 28.0)
];
var promise = Cesium.sampleTerrain(terrainProvider, 11, positions);
Cesium.when(promise, function(updatedPositions) {
    // positions[0].height and positions[1].height have been updated.
    // updatedPositions is just a reference to positions.
});
```
