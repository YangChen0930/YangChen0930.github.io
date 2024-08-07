---
title: 局域网搭建npm私服
date: 2023-07-20 14:39:02
tags:
    - npm
    - 私服

categories:
   - 技术
---

## 背景

出于保密原因后续业务需要内网环境开发，这就要优先解决前端依赖的问题。

## 方案

#### 方案一

将依赖包下载到本地，然后本地安装
**结论**：不友好，而且只能通过文件夹来安装，麻烦
**痛点**： 前端项目依赖多且碎，比如开源依赖又依赖别的开源依赖

<!-- more -->

#### 方案二

经后端同事提醒，可以搭建npm私服来解决。网上搜了一圈发现搭建npm私服软件还挺多的，不过最终选择了使用[nexus](https://www.sonatype.com/products/sonatype-nexus-oss-download)搭建（现在后端同事就是用它搭建maven私服）

搭建方式就不说了，网上一堆说明。

OK。假设我们已经搭建好了npm私服。那么如何使用呢？？？

简单，修改npm配置镜像地址

```bash
npm config set registry=http://xxxxxxxx  //npm 私服源地址
```

然后你就快乐的使用`npm install` 安装依赖

但是你会发现你什么也没安装！！！

因为私服里没有任何npm包，同时因为是内网也无法使用公网镜像，当然什么也没法安装了。

要解决这个问题，两种思路。

1. 暂时开通公网权限，通过设置类型为代理的私库来缓存相关的依赖包。

   npm install时通过设置的代理私库地址拉取公网镜像(taobao、npm)上的依赖，然后这些依赖就会缓存在这个私库里

   ![null](https://s2.loli.net/2024/01/08/gSa9DwreRELtiIN.png)

   ![null](https://s2.loli.net/2024/01/08/2vkw4OJo1fUKF6S.png)

   优点是操作简单

   缺点是每次变更依赖都要开通公网权限

2. 向Nexus上传本地的npm依赖

   一句话概括：先在公网环境下载本地依赖的tgz文件，然后在内网环境上传到nexus

   本地安装完依赖后使用[node_tgz_download](https://www.npmjs.com/package/node-tgz-downloader)下载所有的依赖文件。

   通过`npm publish xxxx`来发布到nexus，最后安装验证是否成功。

![null](https://s2.loli.net/2024/01/08/4D1fNW5ya2kxd3w.png)

![null](https://s2.loli.net/2024/01/08/Dh1ZIYa9MoEKkGe.png)

优点是依赖版本固定，不需要公网权限
缺点是操作麻烦，而且存在依赖下载不全问题，需要不断安装验证，然后手动补全依赖，直到完全安装成功。

![null](https://s2.loli.net/2024/01/08/uPgGiCpBUQIoSVD.png)
![null](https://s2.loli.net/2024/01/08/2cfbWlJ4La1Y7h8.png)

实际应该有602个大package，而最终本地上传的却只有559个大package，缺少部分依赖。

#### 分析

我们是通过分析package-lock.json文件来获取依赖tgz包进行下载的。
package-lock.json文件一般是基于package.json里的`dependencies`和`devDependencies`两个依赖配置项循环迭代生成。

但是有种情况除外，某个package的package.json里配置了`peerDependencies`依赖。

> peerDependencies，也叫同等依赖，或者叫同伴依赖，用于指定当前包（也就是你所要开发的包）使用的这个依赖要兼容的宿主环境中这个依赖的版本。用于解决插件与所依赖包不一致的问题。

有点绕口，翻译下就是如果某个package把我列为依赖的话，那么那个package也必需应该有对peerDependencies里的依赖。

ok，如果正好宿主环境并不需要某个package`peerDependencies`里的依赖，那么它就不会在package-lock里有所体现。那就没法通过package-lock拿到这些依赖的tgz包进行下载。

## 后续

通过`npm i --legacy-peer-deps`命令安装可以跳过`peerDependencies`里的依赖。

安装成功后运行项目，如果成功跑通没有任何报错，那就说明`peerDependencies`里的依赖确实是项目不需要的，可以不用关心；如果失败，根据错误信息按部就班的解决就行。



当然也可以现在公网环境也搭建一套私服，项目依赖全部走私服。然后备份公网私服再在局域网私服同步就行
