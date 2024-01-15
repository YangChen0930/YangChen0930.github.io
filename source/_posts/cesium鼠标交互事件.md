---
title: cesium鼠标交互事件
date: 2022-03-08 17:31:33
tags:
    - webgl
    - cesium
---

## 前言

前面几章我们学过了影像加载、地形加载、模型加载，基于这些已经可以构建一个基本的三维场景，但是光有场景肯定不行，还需要跟场景交互操作，这就轮到鼠标事件出场了，下面是鼠标事件的具体介绍。

## 基础

刚开始查看文档居然没找到鼠标事件，后来查找示例才发现Cesium中鼠标事件是依赖于ScreenSpaceEventHandler对象。

![null](https://s2.loli.net/2024/01/08/iIYfqZ3TtvlALw7.png)

![null](https://s2.loli.net/2024/01/08/lJ7T4a5zmhVAOKZ.png)
ScreenSpaceEventHandler可以监听用户输入事件，所以我们可以设置element为cesium的父容器canvas来实现监听整个页面的效果。

![null](https://s2.loli.net/2024/01/08/mJNUx2FvXLC1O5l.png)
setInputAction可以响应用户的操作，其中第二个参数就是事件类型，查看文档可以发现所有鼠标事件都支持。

![null](https://s2.loli.net/2024/01/08/jAuY3xK41VFyJhr.png)
第三个参数是CTRL、ALT、SHIFT键盘修饰符。
OK，以上就是鼠标事件的基本内容，下面就可以实现具体逻辑了。

```javascript
/**
 * 默认鼠标左键点击事件
 */
const clickHandler = viewer.screenSpaceEventHandler.getInputAction(
  Cesium.ScreenSpaceEventType.LEFT_CLICK
);
// 监听canvas
const handler = new Cesium.ScreenSpaceEventHandler(viewer.scene.canvas);

handler.setInputAction(function (movement) {
  // scene.pick 鼠标选择事件
  const feature =
    viewer.scene.pick(movement.position) &&
    viewer.scene.pick(movement.position).id;
  // 选中对象在cesium中未定义
  if (!Cesium.defined(feature)) {
    clickHandler(movement);
    return;  
  }
  let entities = [x, y, z];
  // 选择模型高亮
  for (let i = 0; i < entities.length; i++) {
    let entity = entities[i];
    entity.polygon.material.color.setValue(that.colorHash[entity.name]);
  }
  feature.polygon.material.color.setValue(Cesium.Color.BLUE);
}, Cesium.ScreenSpaceEventType.LEFT_CLICK);
```

上面代码实现一个简单的点击选择高亮的逻辑，其他鼠标事件都可以照猫画虎实现，没什么难点主要还是响应事件的逻辑不同。

![null](https://s2.loli.net/2024/01/08/2E8pkATuFY3idjH.png)
