---
title: cesium加载地图图层
date: 2022-03-08 16:55:03
tags:
    - webgl
    - cesium

categories:
    - 学习笔记
---

## 1.图层介绍

Cesium中的图层分为两种：一种是普通图层，包含影像、线划等普通显示图层；还有一种是地形图层，用于真实的模拟地球表面的场景，Cesium会根据加载到的地形瓦片以三维的方式显示出山川、大海等。

我们先介绍影像图层，官方提供了丰富的接口给开发者自定义图层。

<!-- more -->

| **接口**                                   | **说明**                                                     |
| ------------------------------------------ | ------------------------------------------------------------ |
| ***ArcGisMapServerImageryProvider\***      | 支持ArcGIS Online和Server的相关服务                          |
| ***BingMapsImageryProvider\***             | Bing地图影像，可以指定mapStyle，详见BingMapsStyle类          |
| ***createOpenStreetMapImageryProvider\***  | OSM影像服务，根据不同的url选择不同的风格                     |
| ***createTileMapServiceImageryProvider\*** | 看文档是根据MapTiler规范，貌似是可以自己下载瓦片，发布服务，类似ArcGIS影像服务的过程 |
| ***GoogleEarthEnteriseImageryProvider\***  | 企业级服务                                                   |
| ***GoogleEarthEnteriseMapsProvider\***     | 企业级服务                                                   |
| ***GridImageryProvider\***                 | 渲染每一个瓦片内部的格网，了解每个瓦片的精细度               |
| ***MapboxImageryProvider\***               | Mapbox影像服务，根据mapId指定地图风格                        |
| ***SingleTileImageryProvider\***           | 单张图片的影像服务，适合离线数据或对影像数据要求并不高的场景下 |
| ***TileCoordinatesImageryProvider\***      | 渲染每一个瓦片的围，方便调试                                 |
| ***UrlTemplateImageryProvider\***          | 指定url的format模版，方便用户实现自己的Provider，比如国内的高德，腾讯等影像服务，url都是一个固定的规范，都可以通过该Provider轻松实现。而OSM也是通过该类实现的。 |
| ***WebMapServiceImageryProvider\***        | 符合WMS规范的影像服务都可以通过该类封装，指定具体参数实现    |
| ***WebMapTileServiceImageryProvider\***    | 服务WMTS1.0.0规范的影像服务，都可以通过该类实现，比如国内的天地图 |

创建图层也能简单，两种方式

```javascript
// 方法1：直接在基础图层上添加图层
viewer.imageryLayers.addImageryProvider(
  new Cesium.UrlTemplateImageryProvider({
      url: "https://webrd03.is.autonavi.com/appmaptile?lang=zh_cn&size=1&scale=1&style=7&x={x}&y={y}&z={z}",
    });
)

// 方法2: 区别在于不是直接加载到viewer的imageryLayers而是scene的imageryLayers
 viewer.scene.imageryLayers.addImageryProvider(
   new Cesium.UrlTemplateImageryProvider({
      url: "https://webrd03.is.autonavi.com/appmaptile?lang=zh_cn&size=1&scale=1&style=7&x={x}&y={y}&z={z}",
    });
 )
```

其实viewer.imagerLayers返回的就是viewer.scene.imagerLayers对象，两者时相等的，所以两者方法本质上没区别。

![null](https://s2.loli.net/2024/01/08/6Z3xMe8tTvJ4O2r.png)
其他创建图层的接口自行查询文档，基本大同小异。

## 2.创建默认图层

之前在[零基础运行Cesium地球](https://videojj-yz.yuque.com/meqmo0/hsvk77/gupvyu?view=doc_embed)一文中介绍了去除默认控件的方法，其中baseLayerPicker是控制图层选择控件的，当设置它为false需要在初始化时手动添加默认图层，否则还是会显示微软图层。方法和创建图层一致。

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
    // terrainProvider: Cesium.createWorldTerrain(),
    imageryProvider: new Cesium.UrlTemplateImageryProvider({
      url: "https://webrd03.is.autonavi.com/appmaptile?lang=zh_cn&size=1&scale=1&style=7&x={x}&y={y}&z={z}"
    }),
  });
```

当然了，在baseLayerPicker为true时，Cesium也提供了方法修改默认图层，在初始化时添加imageryProviderViewModels属性即可。

```javascript
imageryProviderViewModels: imageryLayers//设置影像图列表数组

// index是imagerLayers数组里的某个图层下标
viewer.baseLayerPicker.viewModel.selectedImagery = 
              viewer.baseLayerPicker.viewModel.imageryProviderViewModels[index] 
```



## 3.发布离线地图

以上所有操作都是基于在线地图服务，但也存在局域网的业务场景无法加载在线地图，这就需要我们自己发布地图服务。
图服务发布最常用的是开源工具当属GeoServer，网上一堆资源操作比较复杂，以后有机会再研究，这里介绍一种相对简单的方法，直接把地图数据切片，然后通过静态服务方式发布。

### 第一步：下载原始地图数据

这里我是从[地理空间数据云](https://www.gscloud.cn/sources/accessdata/343?pid=333)随便下的一份数据

### 第二部：切片

下载安装[cesiumLab](http://www.bjxbsj.cn/)工具

![null](https://s2.loli.net/2024/01/08/fhLgouqF491biHt.png)
导入下载的原始地图，存储类型选择散列，生成切片。

![null](https://s2.loli.net/2024/01/08/Ww6rBpGXfjutkPC.png)

### 第三步：部署web服务

web服务很多，比如tomcat、nginx、node等，这里使用nginx来发布。
修改nginx.conf配置文件，静态文件发布我们地图服务，路径就是上面地图切片的的输出路径。

![null](https://s2.loli.net/2024/01/08/YRXKTaHdDitgjZl.png)
使用离线地图

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
    // terrainProvider: Cesium.createWorldTerrain(),
    imageryProvider: new Cesium.UrlTemplateImageryProvider({
          url: '/map/{z}/{x}/{y}.png',
      })
  });
```



## 参考资料

https://www.cxyzjd.com/article/qq_27532167/82590086
https://blog.csdn.net/moyebaobei1/article/details/105420195
