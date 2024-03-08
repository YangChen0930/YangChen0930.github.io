---
title: 零基础运行Cesium地球
date: 2022-03-01 14:33:46
tags:
    - webgl
    - cesium
---

## 什么Cesiumjs

Cesium 是一个跨平台、跨浏览器的展示三维地球和地图的 javascript 库。
Cesium 使用WebGL 来进行硬件加速图形，使用时不需要任何插件支持，但是浏览器必须支持WebGL;
Cesium是基于Apache2.0 许可的开源程序。它可以免费的用于商业和非商业用途。

### 什么是WebGL

WebGL（Web图形库）是一个JavaScript API，可在任何兼容的Web浏览器中渲染高性能的交互式3D和2D图形，而无需使用插件。现在市面上大部分浏览器都支持WebGL。详情见下图

![null](https://s2.loli.net/2024/01/08/gZ6lTXjYLVh1wG8.png)

<!-- more -->

### 环境搭建以及helloworld

Cesium是纯前端的代码，官方给出的源代码中，配套了nodejs的server端，以及可以通过nodejs进行安装部署。实际上也可以将Cesium部署进入tomcat（geoserver）、apache、nginx等服务器中。

#### 运行官方helloworld

1、下载安装[nodejs](https://nodejs.org/en/download/)
2、下载Cesium源码解压
3、进入根目录，运行`npm install、node server.cjs`

![null](https://s2.loli.net/2024/01/08/dWYAbgIsHSTmuBc.png)
打开浏览器，输入`http://localhost:8080`看到下图就是成功

![null](https://s2.loli.net/2024/01/08/GDYs6z2uSEITpdb.png)
输入完整链接`[http://localhost:8080/Apps/HelloWorld.html](http://localhost:8080/Apps/HelloWorld.html)`访问helloworld案例

![null](https://s2.loli.net/2024/01/08/kC37BDUYLA2g6RX.png)

环境搭建好之后，输入 http://localhost:8080/ 有两个链接非常重要
[Documentation](http://localhost:8080/Build/Documentation/index.html)：里面是Cesium的完整的API说明
[Sandcastle](http://localhost:8080/Apps/Sandcastle/index.html)：一个沙箱，可以查看最新的Cesium特性和编写代码测试

## 使用Cesium开发

#### 创建文件

```
mkdir cesium-demo`
`cd cesium-demo
```

#### 引入cesium资源（三种方式）

1. 将Cesium源码中的Build文件夹拷到cesium-demo下，直接引入本地资源

2. cdn引入线上资源

3. 使用 Webpack、Parcel 或 Rollup 等构建应用程序时，

   ```
    npm install cesium
   ```

   安装依赖打包引入

   #### 创建html文件

   ```
   vi index.html
   ```

   打开编辑器，（这里以cdn资源为例）

   新建容器dom

   ```html
   <div id="cesiumContainer"></div>
   ```

   引入js和css资源

   ```html
   <script src="https://cesium.com/downloads/cesiumjs/releases/1.90/Build/Cesium/Cesium.js"></script>
   <link href="https://cesium.com/downloads/cesiumjs/releases/1.90/Build/Cesium/Widgets/widgets.css"rel="stylesheet">
   ```

   初始化地图实例

   ```javascript
   var viewer=newCesium.Viewer("cesiumContainer");
   ```

   ok，简单的地球场景就完成了，打开浏览器查看下吧。

   ### 界面介绍和控件隐藏

![null](https://s2.loli.net/2024/01/08/yQAuCYHlMWeF1NB.png)

1. 查找位置工具，查找到之后会将镜头对准找到的地址，默认使用bing地图

2. 视角返回初始位置

3. 选择视角的模式，有三种：3D，2D，哥伦布视图(CV)

4. 图层选择器，选择要显示的地图服务和地形服务

5. 帮助按钮，显示默认的地图控制帮助

6. 动画器件，控制视图动画的播放速度

7. 时间线,指示当前时间，并允许用户跳到特定的时间

8. 版权显示，显示数据归属，必选

9. 全屏按钮

   ### 隐藏上述小控件

   ##### 方法1 通过js控制

   界面上默认的小控件可以通过在初始化Viewer的时候设置相应的属性来关闭。具体属性可以查看文档。

   ```javascript
   const viewer = new Cesium.Viewer('cesiumContainer', {
   terrainProvider: Cesium.createWorldTerrain(), // 高分辨率地形
   geocoder:false,  // 查找控件
   homeButton:false, // 返回初始位置控件
   sceneModePicker:false, // 选择视角孔金
   baseLayerPicker:false, // 图层选择控件
   navigationHelpButton:false, // 帮助按钮控件
   animation:false, // 动画控件
   timeline:false, // 时间轴控件
   fullscreenButton:false, // 全屏控件
   creditContainer: 'credit'  // 设置版权控件的父级id(需要手动创建一个对应的id的div容器添加到页面中，使用css隐藏父元素)
   });
   viewer._cesiumWidget._creditContainer.style.display = "none"; // 和creditContainer两者选其一
   ```

   ##### 方法2 通过css隐藏

   ```css
      .cesium-viewer-toolbar,             /* 右上角按钮组 */
      .cesium-viewer-animationContainer,  /* 左下角动画控件 */
      .cesium-viewer-timelineContainer,   /* 时间线 */
      .cesium-viewer-bottom               /* logo信息 */
      {
        display: none;
      }
      .cesium-viewer-fullscreenContainer  /* 全屏按钮 */
      { position: absolute; top: -999em;  }
   ```

   > 注：全屏按钮不能通过display:none的方式来达到隐藏的目的，这是因为生成的按钮控件的行内样式设置了display属性，会覆盖引入的css属性

到此一个基本demo就完成了。剩下的就是使用官方提供的工具，完成一些示例，在这些的基础上，自己定制一些内容，或者项目过程中自己的一些思路，以及发现问题，解决问题的一些经验记录。

## 疑问与解答

1. 官方示例引入css有问题

![null](https://s2.loli.net/2024/01/08/3d9P1weOHjGVRCz.png)

![null](https://s2.loli.net/2024/01/08/lMe63wyg49NXhPs.png)

### 两种解决方式

1. webpack配置中设置`alias`别名cesium指向具体文件路径

   ```bash
        然后修改引入路径  
       import "cesium/Widgets/widgets.css";
   ```

![null](https://s2.loli.net/2024/01/08/CjqLPGvfRmTa317.png)

1. 直接使用相对路径引入

   ```bash
     import "./node_modules/cesium/Source/Widgets/widgets.css";
   ```

2. 在Vue中使用遇到的问题优化

- `重要 重要 重要`不要把cesium里的对象如viewer，entity保存到data数据里，会导致劫持数据过万浏览器直接卡崩溃。

  ```bash
   如果需要通信的话，最简单的方法就是挂载到window对象上。
  ```

- 添加本地图片，如使用图片作为material，需要使用import导入图片再将图片赋值给material

  ```javascript
  import rotate from '../assets/logo.png'
  viewer.entities.add({
  rectangle: {
    coordinates: Cesium.Rectangle.fromDegrees(-92.0, 30.0, -76.0, 40.0),
    material: rotate,
    rotation: new Cesium.CallbackProperty(getRotationValue, false),
    stRotation: new Cesium.CallbackProperty(getRotationValue, false),
    classificationType: Cesium.ClassificationType.TERRAIN,
  },
  })
  ```

- 在webpack中设置别名时，目录结构最好只到最外层目录`letcesiumSource='./node_modules/cesium'`，否则在编写代码过程中会没有cesium相关智能提示，很难受！！！

  ```javascript
  import * as Cesium from 'cesium'; // 这样引入就可以实现代码智能提示
  ```

![null](https://s2.loli.net/2024/01/08/D8jn1NhoY3cOQya.png)

### 参考资料

- [cesium官方文档](https://cesium.com/)：cesium的官方教程
- [cesium官方API](https://cesium.com/learn/cesiumjs/ref-doc/)：同样是cesium的官方API文档
- [cesium中文网](http://cesium.xin/)：cesium中文网，里面有系列教程
- [cesium API中文文档](http://cesium.xin/cesium/cn/Documentation1.62/)：翻译的官方文档，不太全初步够用
- [cesium中文社区](http://cesiumcn.org/)：中文cesium社区，有教程