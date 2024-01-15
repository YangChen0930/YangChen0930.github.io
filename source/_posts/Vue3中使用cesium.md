---
title: Vue3中使用cesium
date: 2023-08-04 15:18:10
tags:
    - webgl
    - cesium
    - vue3
    - vite
---

### 1、安装，这里应项目需要安装固定版本

```bash
npm install cesium@1.88 --save
```

### 2、在node_modules/cesium/Build下面复制Assets、ThirdParty、Widgets、Workers文件夹到public文件夹下面。

仅靠源代码是不能运行起 Cesium 三维地球场景的，必须使用构建版本的 CesiumJS 库。而官方构建后的 CesiumJS 库（即发布在 npm 上的 cesium 包）一定会包含以上四类文件，即 node_modules/cesium/Build/ 下的压缩和未压缩版本文件夹下的 Workers、Widgets、Widgets、Assets 四大文件夹。
CESIUM_BASE_URL 的作用，它就是告诉已经运行的 CesiumJS 上哪去找上述四类静态资源。

注意：Vite 开发服务器的根路径，除了挂载了工程的根目录，还挂载了工程根目录下的 public 目录，public 目录的作用请自己查阅 Vite 文档。

### 3、使用

```bash
<template>
  <div id="cesiumContainer"></div>
</template>

<script setup>
  import * as Cesium from 'cesium';
  import '/public/Widgets/widgets.css'

  window.CESIUM_BASE_URL = '/';
  Cesium.Ion.defaultAccessToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiJmYmRjMWNhOC0zN2M1LTRkMTgtYjYwZi1lNzk2ZmY3ZTJkNjkiLCJpZCI6MTU4MjAyLCJpYXQiOjE2OTEwNDMxNjB9.sXxzlZHx1ju-HoMVFZU0tCnRiNtqPaYYm0dm6I3cOfk'

  onMounted(() => {
    const viewer = new Cesium.Viewer('cesiumContainer',{
      //infoBox: false, // 禁用沙箱，解决控制台报错
      // animation: false, // 是否显示动画控件
      // baseLayerPicker: false, // 是否显示图层选择控件
      // vrButton: false, // 是否显示VR控件
      // geocoder: false, // 是否显示地名查找控件
      // timeline: false, // 是否显示时间线控件
      // sceneModePicker: false, // 是否显示投影方式控件
      // navigationHelpButton: false, // 是否显示帮助信息控件
      // navigationInstructionsInitiallyVisible: false, // 帮助按钮，初始化的时候是否展开
```