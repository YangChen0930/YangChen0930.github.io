---
title: web思维导图库对比
date: 2024-08-06 16:57:42
tags:
  - web
  - 思维导图

categories:
  - 随笔
---



## 背景

某项目在演示过程中客户提出需要一个可在线查看类似xmind思维导图的功能。

## 技术选型

先简单分下如果手动实现一个思维导图需要用到什么技术。这种图形类的绘制一般有两种选择：svg和canvas，因为思维导图主要是节点与线的连接，使用与html比较接近的svg比较容易操作。成熟的svg库有很多，比如```RaphaelJS```、```svgjs```和```snap```。

由于要快速交付同时能力有限，主要考虑使用成熟开源的框架来实现对应功能。思维导图主要功能点包括下列几个方面

- 编辑节点（添加、删除、收缩、文字编辑等等）
- 选择节点（单选、多选）
- 画布操作（放大、缩小，拖拽、撤销重做）
- 功能丰富度（标签、关联线等等）

<!-- more -->


### 1、X6

首先想到的就是阿里的```X6```图编辑引擎，在公司其他项目中有用到这个库所以相对比较熟悉。它里面内置了一些常用的图扩展包括思维导图

![2024-08-06_132838_0990170.68035899077397](https://s2.loli.net/2024/08/06/cy4gWYG37s9NbuO.png)

**优点：**

- 实现简单：但是官方提供对应的示例让我们可以轻松的实现思维导图功能
- 可定制化程度高

**缺点：**

- 功能较少
- 丰富功能需要一定的编码能力

### 2、jsmind

这是项目经理提供的一个选择，是基于html5 canvas 和 svg的思维导图类库

使用也能简单传入支持的3种数据格式中一种就可以得到一个只读的思维导图，想要编辑功能需要通过API手动实现。

```
<html>
    <head>
        <link
            type="text/css"
            rel="stylesheet"
            href="//cdn.jsdelivr.net/npm/jsmind@0.8.5/style/jsmind.css"
        />
        <script
            type="text/javascript"
            src="//cdn.jsdelivr.net/npm/jsmind@0.8.5/es6/jsmind.js"
        ></script>
    </head>
    <body>
        <div id="jsmind_container"></div>

        <script type="text/javascript">
            var mind = {
                // 3 data formats were supported ...
                // see documents for more information
            };
            var options = {
                container: 'jsmind_container',
                theme: 'orange',
                editable: true,
            };
            var jm = new jsMind(options);
            jm.show(mind);
        </script>
    </body>
</html>
```

![](https://s2.loli.net/2024/08/06/YcLdo1mHbFyuCsN.png)

**优点：**

- 兼容性好
- 支持图片节点


**缺点：**

- 功能较少
- 界面美观度一般

### 3、vue3-mindmap

Vue3的思维导图组件

使用特别简单直接引入即可

```
<template>
  <mindmap v-model="data"></mindmap>
</template>

<script>
import mindmap from 'vue3-mindmap'
import 'vue3-mindmap/dist/style.css'

export default defineComponent({
  components: { mindmap },
  setup () => {
    const data = [{
      "name":"如何学习D3",
      "children": [
        {
          "name":"预备知识",
          "children": [
            { "name":"HTML & CSS" },
            { "name":"JavaScript" },
            ...
          ]
        },
        {
          "name":"安装",
          "collapse": true,
          "children": [ { "name": "折叠节点" } ]
        },
        { "name":"进阶", "left": true },
        ...
      ]
    }]

    return { data }
  }
})
</script>
```

![](https://s2.loli.net/2024/08/06/h7skun1oOKIEvUy.png)

**优点：**

- 使用简单，功能相对齐全

**缺点：**

- 停止更新
- 强依赖vue3（虽然在本项目中不算缺点）
- 不可扩展

### 4、mind-elixir-core

一个无框架依赖的思维导图内核

![](https://s2.loli.net/2024/08/06/Rr9vlyxNV1gf2J7.png)

**优点：**

- 使用简单，功能相对齐全
- 支持关联和汇总节点
- 可自定义每个节点样式
- 支持插件，可自定义功能

**缺点：**

- 右键拖动画布，有点反常规
- 文档简陋


### 5、mind-map

一个还算强大的Web思维导图

![](https://s2.loli.net/2024/08/06/k7vZe9LC1c5I4s6.png)

**xmind文件**
![xmind文件](https://s2.loli.net/2024/08/07/KkpzhgyUvMO2oai.png)
**JSON文件**
![JSON文件](https://s2.loli.net/2024/08/07/LTwtbBOrCnHcsUW.png)

**优点：**

- 相对齐全的思维导图功能（目前几个开源框架中功能最多的）
- 支持读取和导出XMind文件
- 支持协同编辑和演示模式
- 定制化程度高
- 文档完善


**缺点：**

- 并没有提供一个完整思维导图，需要根据提供的接口来实现更多功能（项目附带一个vue2+elementui实现的完整思维导图）
- 不支持概要节点后面继续添加节点
- 不支持多个根节点
- 导入xmind文件会丢失关联线（JSON文件并不会）


### 6、mindmaptree

一个基于web(svg)的思维导图

```
<body>
  <div id="container" style="width:100vh;height:100vh;"></div>
</body>

import MindmapTree from 'mindmap-tree';
import 'mindmap-tree/style.css';

new MindmapTree({
  container: '#container',
  // 节点数据
  data: []
});
```

![](https://s2.loli.net/2024/08/06/BZPSCtwVes9Ep53.png)

**优点：**

- 使用简单

**缺点：**

- 功能较少
- 需要选中根节点才能拖拽画布
- 不可扩展
- 文档简陋不全

## 结论

首先不考虑```jsMind```，毕竟美观是影响人的第一要数，剩下的需要根据具体需求的不同来取舍。

比如只需要可编辑节点文本，无其他复杂逻辑的思维导图，这时可以选择```X6```，如果是用的Vue3框架就用```vue3-mindmap```

再比如需求是一个完整的思维导图，那完全可以选择```mind-map```和```mind-elixir-core```。同样视功能点和排期的不同，它两之间还可以再做区分。（对功能点的细节要求不高可以用```mind-elixir-core```）。

总的来说在开源的思维导图里，```mind-map```可能相对好用些。

以上~~
