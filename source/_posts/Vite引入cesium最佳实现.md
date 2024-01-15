---
title: Vite引入cesium最佳实现
date: 2023-09-25 14:20:11
tags:
    - webgl
    - cesium
    - vite
---

本文是基于[Vue3(vite)中使用cesium](/2023/08/04/Vue3中使用cesium/)实现的优化方案，如果还没了解过如何在Vite中使用CesiumJS，请自行查看。

假设我们已经对如何在Vue3中使用CesiumJS有所了解，但还是有如下问题存在

- Vite 仍需对毫无修改的 cesium 依赖包打包一次，CesiumJS 已经在发布 npm 包时进行了构建，其虽然有 ESModule 格式的产物，但是并不支持 Tree-Shaking 减小大小，事实上也没有必要
- 需要手动复制 node_modules/cesium/Build/CesiumUnminified/ 下的四个静态资源文件夹
- 对多个发布环境仍需要手动修改 CESIUM_BASE_URL，如果切换到 CDN 或内网已有 CesiumJS 在线库资源，这个改起来就麻烦许多

## 优化点

### 使用插件外部化CesiumJS

Vite 和 Webpack 类似，都能把一些依赖无视，不参与打包，一旦某个依赖被配置为“外部的”，即 External 化，就不会打包它了。

插件 `vite-plugin-externals`

安装

```bash
npm i -D vite-plugin-externals
```

使用

```bash
import { viteExternalsPlugin } from 'vite-plugin-externals'

export default defineConfig({
    plugins: [
        // 其他插件
        viteExternalsPlugin(
        {
          // key 是要外部化的依赖名，value 是全局访问的名称，这里填写的是 'Cesium'
          // 意味着外部化后的 cesium 依赖可以通过 window['Cesium'] 访问；
          // 支持链式访问，参考此插件的文档
          cesium: 'Cesium'
        },
        {
          disableInServe: true // 开发模式时不外部化
        }
      ),
    ]
})
```

> Vite 启动后会有一个依赖预构建的过程，打开 node_modules/.vite/deps 目录，这里就是预构建的各种代码中导入的依赖包

开发模式下外部化会报找不到模块错误。

执行`npm run build`后`npm run preivew`预览，还是会报找不到对应类错误

![null](https://s2.loli.net/2024/01/08/WLis2pfB1v3xmPQ.png)

这是因为外部化CesiumJS后，便不再打包cesium相关 依赖，所以打包后的应用找不到CesiumJS 的类了。

那么只需打包时把 CesiumJS 的主库文件导入 index.html 不就行了吗

### 使用插件自动在 index.html 引入 Cesium.js 库文件

其实可以直接手动在index.html中引入Cesiumjs文件，在index.html文件里添加一行script标签

```bash
<head>
  <script src="libs/cesium/Cesium.js"></script>
</head>
```

只不过这样， 执行打包时会收到 Vite 的一句警告，同时也不够灵活

![null](https://s2.loli.net/2024/01/08/Sqwy13slcka5Xnz.png)

可以通过vite插件在打包过程中往index.html里插入我们需要的script，这类插件有很多，这里以`vite-plugin-insert-html`为例

```bash
import { insertHtml, h } from 'vite-plugin-insert-html'

export default defineConfig({
    plugins: [
    // 其他插件
    insertHtml({
        head: [
          h('script', {
            // 因为涉及前端路径访问，所以开发模式最好显式拼接 base 路径，适配不同 base 路径的情况
            src: isProd ? `${base}${cesiumBaseUrl}Cesium.js` : `${cesiumBaseUrl}Cesium.js`
          }),
          h('link', {
            rel: 'stylesheet',
            href: isProd ? `${base}${cesiumBaseUrl}Widgets/widgets.css` : `${cesiumBaseUrl}Widgets/widgets.css`
          })
        ]
      })
    ]
})
```

这样就能解决上述问题

![null](https://s2.loli.net/2024/01/08/AB4JgxIKCFl1hLw.png)

但是，到这里还是需要手动复制四大静态文件和CesiumJS库文件，这同样可以通过插件从node_modules里复制文件到指定路径

### 四大静态文件夹与库文件的拷贝

我们可以拿Vite 的静态文件复制插件完成这一步。这里用`vite-plugin-static-copy`做示例。

```bash
import { viteStaticCopy } from 'vite-plugin-static-copy'

export default defineConfig({
    plugins: [
        // 其他插件
        viteStaticCopy({
        targets: [
          {
            src: 'node_modules/cesium/Build/CesiumUnminified/Cesium.js',
            dest: 'lib/cesium/'
          },
          {
            src: 'node_modules/cesium/Build/CesiumUnminified/Assets/*',
            dest: 'lib/cesium/Assets/'
          },
          {
            src: 'node_modules/cesium/Build/CesiumUnminified/ThirdParty/*',
            dest: 'lib/cesium/ThirdParty/'
          },
          {
            src: 'node_modules/cesium/Build/CesiumUnminified/Workers/*',
            dest: 'lib/cesium/Workers/'
          },
          {
            src: 'node_modules/cesium/Build/CesiumUnminified/Widgets/*',
            dest: 'lib/cesium/Widgets/'
          }
        ]
      }),
    ]
})
```

上面的代码还可以继续优化，这个 target 中很多路径都是相同的，可以通过数组计算完成。dest 是打包后的根路径的相对路径。

OK，到此为止已经可以灵活使用CesiumJS了。当然我们还可以通过环境变量来兼容局域网或已经部署好的CesiumJS库这类情况。
