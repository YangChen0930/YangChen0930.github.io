---
title: 快速导出网页内容为word文档
date: 2023-03-03 09:39:58
tags:
    - web
    - 导出
---

## 前言

最近遇到个需求，某预览页面可以导出成word文档以便用户直接使用。其实这个功能前后端都可以实现，我们项目定的结论是后端生成word文档，前端只做预览页面。

当然这个结论是需求澄清后给出的，在此之前我都是认为需要前端根据html文档生成word，所以做了些调研，在这里记录下做些分享。

## 前端插件

网上找到一些html转word的插件，分别使用demo测试结果如下：

这是html页面呈现内容

![null](https://s2.loli.net/2024/01/08/l7Y3G1oJ5Ojs64R.png)

插件

生成word内容

结论

[jquery.googoose](https://github.com/aadel112/googoose)

![null](https://s2.loli.net/2024/01/08/yTA5GVaz62PgdmE.png)

echarts图表丢失

[jquery.wordexport](https://github.com/markswindoll/jQuery-Word-Export)

![null](https://s2.loli.net/2024/01/08/zTaNS6pjWcR2qQX.png)

echarts图表丢失，表格边框丢失

[export-word](https://github.com/huangbohang/export-word)

![null](https://s2.loli.net/2024/01/08/zWGvMqi7SZ3uCwD.png)

基本内容都有，echarts图表格式不对，不过应该是word宽度的限制

从上面来看基本的html转word前端是可以实现的，主要差别在于一些非文本、表格、图表的内容上，而针对这些不支持的内容一个方法是把它们转成图片来插入文档解决。

对比了以上三个插件的源码，主要不同点就是export-word多了个使用canvas转成图表功能

![null](https://s2.loli.net/2024/01/08/v45FMR93qBVUbJX.png)

![null](https://s2.loli.net/2024/01/08/b8sS1Q2PKlhGeiL.png)

![null](https://s2.loli.net/2024/01/08/UZlP1MDYwk6R2EW.png)

## 后端实现

具体的后端实现我不太清楚，不过后端的难点跟前端是一致的，对于一些图表、非正常的表格等都无法获取，需要依赖前端将这些内容转成图片传给后端，然后插入文档。

## 结论

其实这么来说，不管前端实现还是后端实现都是一样的，难点在于非文本的一些结构如何添加到word里，解决方法都是一样的。