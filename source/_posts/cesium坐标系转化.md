---
title: cesium坐标系转化
date: 2022-03-14 17:16:51
tags:
    - webgl
    - cesium
---

## 前言

涉及到地图开发肯定离不开坐标系相关知识，关于国内的坐标系转换和常用坐标系自行查询资料。
[地理坐标系](https://juejin.cn/post/6930539078488326152#heading-17)、[投影坐标系](https://juejin.cn/post/6940684126282317861)

Cesium中常用的坐标有两种：WGS84坐标和笛卡尔空间坐标，我们平时以经纬度来指向一个地点用的就是WGS84坐标，笛卡尔空间坐标则常用来做一些空间位置的变换，如平移、缩放等。

## 坐标系转化

### 经纬度和弧度的转化

```json
var radians=Cesium.Math.toRadians（degrees）;//经纬度转弧度
var degrees=Cesium.Math.toDegrees（radians）;//弧度转经纬度
```

<!-- more -->

### WGS84经纬度坐标和WGS84弧度坐标系转化

```json
//方法一：
var longitude = Cesium.Math.toRadians(longitude1); //其中 longitude1为角度
var latitude= Cesium.Math.toRadians(latitude1); //其中 latitude1为角度
var cartographic = new Cesium.Cartographic(longitude, latitude, height)；

//方法二：其中，longitude和latitude为经纬度
var cartographic= Cesium.Cartographic.fromDegrees(longitude, latitude, height);

//方法三：其中，longitude和latitude为弧度
var cartographic= Cesium.Cartographic.fromRadians(longitude, latitude, height);
```

### WGS84坐标系和笛卡尔空间直角坐标系转化

```json
var position = Cesium.Cartesian3.fromDegrees(longitude, latitude, height)；//其中，高度默认值为0，可以不用填写；longitude和latitude为经纬度

var positions = Cesium.Cartesian3.fromDegreesArray(coordinates);//其中，coordinates格式为不带高度的数组。例如：[-115.0, 37.0, -107.0, 33.0]

var positions = Cesium.Cartesian3.fromDegreesArrayHeights(coordinates);//coordinates格式为带有高度的数组。例如：[-115.0, 37.0, 100000.0, -107.0, 33.0, 150000.0]

//同理，通过弧度转换，用法相同，具体有Cesium.Cartesian3.fromRadians，Cesium.Cartesian3.fromRadiansArray，Cesium.Cartesian3.fromRadiansArrayHeights等方法
```

### 笛卡尔空间直角坐标系转换WGS84转化

```json
var cartographic= Cesium.Cartographic.fromCartesian(cartesian3)；
```
