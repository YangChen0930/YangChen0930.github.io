---
title: cesium加载矢量数据
date: 2022-03-08 17:20:28
tags:
    - webgl
    - cesium

categories:
    - 学习笔记
---

## 1.加载GeoJson、KML、CZML数据

这几类数据都是矢量数据，所以这里放到一起介绍

### 1.1GeoJson

GeoJson是一种基于JSON的地理空间数据交换格式，它定义了几种类型JSON对象以及它们组合在一起的方法，以表示有关地理要素、属性和它们的空间范围的数据。

Cesium加载GeoJson的原理是先load数据，然后在对load后的entity逐一设置。具体方法如下：

```javascript
let dataSource = new Cesium.GeoJsonDataSource();

var promise = dataSource.load(jiangsu); // json路径，load后得到一个promise对象

promise.then((datasource) => {
  viewer.dataSources.add(datasource); // 加入到场景中
  var entities = datasource.entities.values; // 
  for (var i = 0; i < entities.length; i++) {
    var entity = entities[i];
    var name = entity.name;
    let color = that.colorHash[name];
    if (!color) {
      color = Cesium.Color.fromRandom({ alpha: 0.5 });
      that.colorHash[name] = color;
    }
    entity.polygon.material = color;
    entity.polygon.outline = false;
    entity.polygon.extrudedHeight = entity.properties.area;
  }
```

以上代码生成了江苏省各市的地形图

![null](https://s2.loli.net/2024/01/08/ThFqoA6jfSVdmsr.png)

<!-- more -->

### 1.2 KML

KML是基于XML语法标准的一种标记语言，采用标记结构，含有嵌套的元素和属性，应用说Google Earth、Google Map等地图应用中。

其加载方式跟GeoJson相同，剩下的处理逻辑也和GeoJson一致，这里不做过多说明。

```javascript
var promise = Cesium.KmlDataSource.load('data.kml');C
```

### 1.3CZML

CZML是Cesium定义的一种描述动态场景的JSON结构，它可以用来描述点、线、布告板、模型以及其他的图元，同时定义他们是怎样随时间变化的。Cesium与CZML的关系就如同Google Earth和KML的关系。

一个基础的CZML结构如下：

```json
[
  {
    "id":"document",
    "version":"1.0"
  },
  {
    "id":"Vehicle"
    //
  }
]
```

CZML时间相关属性如下，注意，这里的时间为UTC（协调世界时），而我们国家通常使用的是UTC+8也就是北京时间，所以要注意。

```json
{
    // ...  
    "someInterpolatableProperty": {  
        "cartesian": [  
            "2012-04-30T12:00Z", 1.0, 2.0, 3.0, //表示当时间为2012-04-30T12:00Z，坐标为(1,2,3)
            "2012-04-30T12:01Z", 4.0, 5.0, 6.0, //表示当时间为2012-04-30T12:01Z，坐标为(4,5,6)
            "2012-04-30T12:02Z", 7.0, 8.0, 9.0  //表示当时间为2012-04-30T12:02Z，坐标为(7,8,9)
        ]  
    }  
}
{  
    // ...  
    "someInterpolatableProperty": {  
        "epoch": "2012-04-30T12:00Z", //表示时间起点为2012-04-30T12:00：00 
        "cartesian": [  
            0.0, 1.0, 2.0, 3.0,  //从起点开始，第0秒时坐标为(1,2,3)
            60.0, 4.0, 5.0, 6.0, //从起点开始，第60秒时坐标为(4,5,6) 
            120.0, 7.0, 8.0, 9.0 //从起点开始，第120秒时坐标为(7,8,9) 
        ]  
    }  
}
```

| 名称                   | Scope 范围 | JSON类型         | 说明                                                         |
| ---------------------- | ---------- | ---------------- | ------------------------------------------------------------ |
| epoch                  | Packet     | string           | 使用ISO8601规范来表示日期和时间                              |
| nextTime               | Packet     | string or number | 在时间间隔内下一个采样的时间，可以通过ISO8061方式，也可以通过与epoch秒数来定义。它决定了不同packet之间的采样是否有停顿。 |
| previousTime           | Packet     | String or number | 在时间间隔内前一个采样的时间，可以通过ISO8061方式，也可以通过与epoch秒数来定义，它决定了不同packet之间的采样是否有停顿。 |
| InterpolationAlgorithm | Interval   | String           | 用于插值的算法，有LAGTANGE，HERMITE和GEODESIC。默认是LAGRANGE。如果位置不在该采样区间，那么这个属性值会被忽略。 |
| interpolationDegree    | Interval   | Number           | 定义了用来插值所使用的多项式的次数，1表示线性差值，2表示二次插值法，默认为1。如果使用GEODESIC插值算法，那么这个属性将被忽略。 |

其他具体介绍可以查阅相关资料。CZML的加载处理和上面两种类型一样。

```json
dataSource = new Cesium.CzmlDataSource();
var czml = 'data/Vehicle.czml';
dataSource.load(czml);
```

## 2.加载3D Tiles

当加载大量的三维对象数据时，我们当然可以使用上面三种方式加载，但是性能得不到保障。
有没有更好的解决方案呢？？？有，Cesium提供的3D Tiles格式。传统的倾斜摄影、点云、三维模型数据都可以通过工具转换成3D Tiles格式。

3D Tiles可以显示建筑物、地标乃至森林广告牌等等以及其对应的属性信息。每个3DTiles就是一个3D对象，具体的数据范围等等信息在tileset.json中定义。

### 2.1 加载

```json
 var tileset = viewer.scene.primitives.add(new Cesium.Cesium3DTileset({
     url: 'xxxxx/tileset.json'
 }));

tileset.readyPromise.then(function () {
  viewer.camera.viewBoundingSphere(tileset.boundingSphere, new Cesium.HeadingPitchRange(0, -0.5, 0));
  viewer.camera.lookAtTransform(Cesium.Matrix4.IDENTITY);
}).otherwise(function (error) {
  // xxxxx
});
```

### 2.2 Style

3D Tiles 样式语言可定义数据集中要素显示的规则，包括颜色、显示与否等等。

```json
osmBuildingsTileset.style = new Cesium.Cesium3DTileStyle({
  defines: {
    distanceFromComplex:
      "distance(vec2(${feature['cesium#longitude']}, ${feature['cesium#latitude']}), vec2(144.96007, -37.82249))",
  },
  color: {
    conditions: [
      ["${distanceFromComplex} > 0.010", "color('#d65c5c')"],
      ["${distanceFromComplex} > 0.006", "color('#f58971')"],
      ["${distanceFromComplex} > 0.002", "color('#f5af71')"],
      ["${distanceFromComplex} > 0.0001", "color('#f5ec71')"],
      ["true", "color('#ffffff')"],
    ],
  },
});
```

注意conditions中条件必须闭合，不能出现分类不完整，所以一般最后会加一个true项，相当于default。

生成如下效果

![null](https://s2.loli.net/2024/01/08/5Nr4yYbtiHRd6Jk.png)

### 2.3 支持的格式

3D tiles是由tileset.json和tile文件构成，tile文件格式可以是.b3dm、.i3dm、.pnts、.vctr和.cmpt中的任一种。

| **格式** | **说明**                           |
| -------- | ---------------------------------- |
| b3dm     | 用于展示城市建筑等大规模的3D对象   |
| l3dm     | 用于展示树、风力发电机模型等       |
| pnts     | 用于展示大量的3D点                 |
| vctr     | 用于展示矢量元素，代替KML？        |
| cmpt     | 以上不同格式的切片组合到一个切片中 |

tileset.json里的具体字段自行查阅官方文档。
