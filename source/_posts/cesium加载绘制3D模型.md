---
title: cesium加载绘制3D模型
date: 2022-03-08 17:13:35
tags:
    - webgl
    - cesium
---

## 1.加载模型

使用很简单，就是现今常用的三维建模工具（3dmax、maya、creator、sketch up等等）都不支持gltf格式文件的直接导出，需要经过中间转化。

```javascript
const entity = viewer.entities.add({
    id: "car",
    name: "car",
    availability: new Cesium.TimeIntervalCollection([ new Cesium.TimeInterval({start: start, stop: stop})]),
    position: property,
    orientation: new Cesium.VelocityOrientationProperty(property),
    model: {
      uri: "./GroundVehicle.glb", // 模型
      minimumPixelSize: 64,
      maximumSize: 128,
      maximumScale: 200,
      scale: 16,
      runAnimations: true
    },
  });
  viewer.trackedEntity = entity; // 镜头固定到模型上
```

<!-- more -->

![null](https://s2.loli.net/2024/01/08/iwYOa5sIqANLHte.png)

## 2.绘制模型

Cesium不只可以加载3d模型，还可以基于空间三维数据绘制模型。Cesium提供了两类接口来绘制模型，entity和primitive。

![null](https://s2.loli.net/2024/01/08/p4N8oGC6HSuVJMj.png)
![null](https://s2.loli.net/2024/01/08/KuwHlsGnDYLBOhx.png)
两者的区别其实是：entity是个相对高级的接口，是个prmitive的封装过的语法糖；primitive更接近渲染引擎底层相对更复杂，包含geometry和appearance两部分，geometry定义primitive的几何结构，appearance定义primitive的着色。



### 2.1.基本绘制

#### 2.1.1 entity绘制

```javascript
viewer.entities.add({
   name: 'red box',
   position: Cesium.Cartesian3.fromDegrees(-107.0, 40.0, 300000.0),
   box: {
     dimensions: new Cesium.Cartesian3(40000, 30000, 50000),
     material: Cesium.Color.RED.withAlpha(0.5),
     outline: true,
     outlineColor: Cesium.Color.BLACK
   }
});
```

可以看到entity通过viewer中的entities加载到场景中，entities是entity的集合对象。
以下是官方支持的形状：

| **形状**               | **说明**                                                     |
| ---------------------- | ------------------------------------------------------------ |
| Point                  | 点；[文档](https://cesium.com/learn/cesiumjs/ref-doc/PointGraphics.html?classFilter=point) |
| Boxes                  | 盒子；[文档](https://cesium.com/learn/cesiumjs/ref-doc/BoxGraphics.html?classFilter=box)；[示例](https://sandcastle.cesium.com/index.html?src=Box.html) |
| Circles and Ellipses   | 圆和椭圆；[文档](https://cesium.com/learn/cesiumjs/ref-doc/EllipseGraphics.html?classFilter=elli)；[示例](https://sandcastle.cesium.com/index.html?src=Circles and Ellipses.html) |
| Corridor               | 走廊通道；[文档](https://cesium.com/learn/cesiumjs/ref-doc/CorridorGraphics.html?classFilter=corr)；[示例](https://sandcastle.cesium.com/index.html?src=Corridor.html) |
| Cylinder and cones     | 圆柱和圆锥；[文档](https://cesium.com/learn/cesiumjs/ref-doc/CylinderGraphics.html?classFilter=cylin)；[示例](https://sandcastle.cesium.com/index.html?src=Cylinders and Cones.html) |
| Polygons               | 多边形；[文档](https://cesium.com/learn/cesiumjs/ref-doc/PolygonGraphics.html?classFilter=polyg)；[示例](https://sandcastle.cesium.com/index.html?src=Polygon.html) |
| Polylines              | 折线；[文档](https://cesium.com/learn/cesiumjs/ref-doc/PolylineGraphics.html?classFilter=polyl)；[示例](https://sandcastle.cesium.com/index.html?src=Polyline.html) |
| Polyline volumes       | 折线卷，折线体积；[文档](https://cesium.com/learn/cesiumjs/ref-doc/PolylineVolumeGraphics.html?classFilter=polyl)；[示例](https://sandcastle.cesium.com/index.html?src=Polyline Volume.html) |
| Rectangles             | 矩形；[文档](https://cesium.com/learn/cesiumjs/ref-doc/RectangleGraphics.html?classFilter=rec)；[示例](https://sandcastle.cesium.com/index.html?src=Rectangle.html) |
| Spheres and ellipsoids | 球体和椭圆体；[文档](https://cesium.com/learn/cesiumjs/ref-doc/EllipsoidGraphics.html)；[示例](https://sandcastle.cesium.com/?src=Spheres and Ellipsoids.html) |
| Walls                  | 墙体；[文档](https://cesium.com/learn/cesiumjs/ref-doc/WallGraphics.html)；[示例](https://sandcastle.cesium.com/?src=Wall.html) |

#### 2.1.2 primitive绘制

```javascript
var instance = new Cesium.GeometryInstance({
   geometry: new Cesium.BoxGeometry({
        vertexFormat : Cesium.VertexFormat.POSITION_ONLY,
        maximum : new Cesium.Cartesian3(250000.0, 250000.0, 250000.0),
        minimum : new Cesium.Cartesian3(-250000.0, -250000.0, -250000.0)
    })
});

viewer.scene.primitives.add(new Cesium.Primitive({
      geometryInstances: instance,
      appearance: new Cesium.EllipsoidSurfaceAppearance({
          material:Cesium.Material.fromType('Stripe')

      })
}));
```

primitive通过viewer中的primitives加载到场景中，primitives是primitives的集合对象。

**Geometry**
Cesium支持以下几种Geometry几何图形

| 几何图形                      | 说明                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| BoxGeometry                   | 立方体                                                       |
| BoxOutlineGeometry            | 仅有轮廓的立方体，只有外部线条的的盒子                       |
| CircleGeometry                | 圆形或者拉伸的圆形，圆圈或挤压圆                             |
| CircleOutlineGeometry         | 只有轮廓的圆形                                               |
| CorridorGeometry              | 走廊：沿着地表的多段线(垂直于表面的折线)，且具有一定的宽度，可以拉伸到一定的高度 |
| CorridorOutlineGeometry       | 只有轮廓的走廊                                               |
| CylinderGeometry              | 圆柱、圆锥或者截断的圆锥                                     |
| CylinderOutlineGeometry       | 只有轮廓的圆柱、圆锥或者截断的圆锥                           |
| EllipseGeometry               | 椭圆或者拉伸的椭圆                                           |
| EllipseOutlineGeometry        | 只有轮廓的椭圆或者拉伸的椭圆                                 |
| EllipsoidGeometry             | 椭球体                                                       |
| EllipsoidOutlineGeometry      | 只有轮廓的椭球体                                             |
| RectangleGeometry             | 矩形或者拉伸的矩形                                           |
| RectangleOutlineGeometry      | 只有轮廓的矩形或者拉伸的矩形                                 |
| PolygonGeometry               | 多边形，可以具有空洞或者拉伸一定的高度                       |
| PolygonOutlineGeometry        | 只有轮廓的多边形                                             |
| PolylineGeometry              | 多段线，可以具有一定的宽度                                   |
| SimplePolylineGeometry        | 简单的多段线                                                 |
| PolylineVolumeGeometry        | 多段线柱体                                                   |
| PolylineVolumeOutlineGeometry | 只有轮廓的多段线柱体                                         |
| SphereGeometry                | 球体                                                         |
| SphereOutlineGeometry         | 只有轮廓的球体                                               |
| WallGeometry                  | 墙                                                           |
| WallOutlineGeometry           | 只有轮廓的墙                                                 |

在上例代码中我们用到了几何图形实例，多个实例可以公用一个geometry，通过设置GeometryInstance.modelMatrix 提供不同的信息。

```javascript
var geometry = Cesium.BoxGeometry.fromDimensions({
  vertexFormat : Cesium.VertexFormat.POSITION_AND_NORMAL,
  dimensions : new Cesium.Cartesian3(1000000.0, 1000000.0, 500000.0)
});
var instanceBottom = new Cesium.GeometryInstance({
  geometry : geometry,
  modelMatrix : Cesium.Matrix4.multiplyByTranslation(Cesium.Transforms.eastNorthUpToFixedFrame(
    Cesium.Cartesian3.fromDegrees(-75.59777, 40.03883)), new Cesium.Cartesian3(0.0, 0.0, 1000000.0), new Cesium.Matrix4()),
  attributes : {
    color : Cesium.ColorGeometryInstanceAttribute.fromColor(Cesium.Color.AQUA)
  },
  id : 'bottom'
});
var instanceTop = new Cesium.GeometryInstance({
  geometry : geometry,
  modelMatrix : Cesium.Matrix4.multiplyByTranslation(Cesium.Transforms.eastNorthUpToFixedFrame(
    Cesium.Cartesian3.fromDegrees(-75.59777, 40.03883)), new Cesium.Cartesian3(0.0, 0.0, 3000000.0), new Cesium.Matrix4()),
  attributes : {
    color : Cesium.ColorGeometryInstanceAttribute.fromColor(Cesium.Color.AQUA)
  },
  id : 'top'
});
```

同时多个实例也可以合并成一个primitive，节约性能。

```javascript
var instances = [Instance1, Instance2, Instance3, ……];
scene.primitives.add( new Cesium.Primitive( {
    geometryInstances : instances, //合并
    //某些外观允许每个几何图形实例分别指定某个属性，例如：
    appearance : new Cesium.PerInstanceColorAppearance({translucent : false,closed : true})
}));
```

**Appearance**
Cesium支持下列appearance

| **外观**                   | **说明**                                                     |
| -------------------------- | ------------------------------------------------------------ |
| MaterialAppearance         | 支持各种Geometry类型的外观，支持使用材质来定义着色。支持材料描述阴影。 |
| EllipsoidSurfaceAppearance | MaterialAppearance的一个版本。假设几何图形与地表是平行的，并且依此来进行顶点属性（vertex attributes）的计算。和Material Appearance一样，就像一个多边形，并且使用这个假设来通过程序上计算许多顶点属性来节省内存。 |
| PerInstanceColorAppearance | 让每个实例使用自定义的颜色来着色，使用每个实例的颜色来遮蔽每个实例。 |
| PolylineMaterialAppearance | 支持使用材质来着色多段线。支持材料遮蔽Polyline               |
| PolylineColorAppearance    | 使用每顶点或者每片段（per-vertex or per-segment ）的颜色来着色多段线—使用每顶点或每段着色来遮蔽折线 |

Appearance定义了需要在GPU上执行的GLSL着色器，这部分一般只有在自定义外观时需要修改。

### 2.2.管理

#### 2.2.1 entity管理

viewer.entities属性实际上是一个EntityCollection对象，是entity的一个集合，提供了add、remove、removeAll等等接口来管理场景中的entity。

![null](https://s2.loli.net/2024/01/08/xBoEJyDrGZ9SMOU.png)
Cesium.EntityCollection.collectionChangedEventCallback(collection,added, removed, changed)
add(entity) → Entity
computeAvailability() → TimeInterval
contains(entity) → Boolean
getById(id) → Entity
getOrCreateEntity(id) → Entity
remove(entity) → Boolean
removeAll()
removeById(id) → Boolean
resumeEvents()
suspendEvents()

#### 2.2.2 primitive管理

场景通过viewer.scene.primitives属性来管理添加的primitive对象，primitives是PrimitiveCollection类型

![null](https://s2.loli.net/2024/01/08/kq5UdluSYBXApTe.png)
提供了如下接口管理primitive对象：
add(primitive) → Object
contains(primitive) → Boolean
destroy() → undefined
get(index) → Object
isDestroyed() → Boolean
lower(primitive)
lowerToBottom(primitive)
raise(primitive)
raiseToTop(primitive)
remove(primitive) → Boolean
removeAll()

### 2.3.拾取选择

通常我们不光要绘制模型，还要跟场景进行交互，Cesium为我们提供了scene.pick接口进行拾取，具体介绍放到单独的鼠标事件一章中。

### 2.4.两者对比

既然已经有了entity为什么还要在提供个primitive接口呢？？？entity调用方便，封装完美，什么时候要用primitive？？？
答案是性能问题，primitve更接近webGL的底层，加载效率更高，具体分析参考
https://juejin.cn/post/6972420331982028837