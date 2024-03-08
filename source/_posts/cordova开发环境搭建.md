---
title: cordova开发环境搭建
date: 2019-07-27 14:33:48
tags:
  - app
  - web
  - cordova
---

工作中接触过几次混合 APP 开发，所以将一些用得到的内容记录下来，乙方遗忘。

# hybrid 简易开发环境搭建

## 软件安装

### JDK

[Java SE Downloads](http://www.oracle.com/technetwork/java/javase/downloads/index.html)

#### 环境变量

JDK

```
JAVA_HOME
D:\Program Files\Java\jdk1.8.0_172
PATH
%JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;
CLASSPATH
.;%JAVA_HOME%\lib\dt.jar;%JAVA_HOME%\lib\tools.jar;
```

<!-- more -->

### Android Studio

[Download Android Studio](https://developer.android.google.cn/studio/)

[安装 Android Studio](https://developer.android.com/studio/install?hl=zh-cn)

#### 配置代理

安装过程中弹出配置代理的提示框
选择手动配置代理
安装过程中的提示框配置，与下图(下图为安装完成后 File->Settings 的截图)，类似

![2018-07-06-10-08-14](https://s2.loli.net/2024/01/08/owZVFf8Herh1s6T.png)

**Android Studio 在构建应用的过程中需要下载 SDK, Gradle, build tools 等等**

#### 环境变量

![2018-07-06-10-26-01](https://s2.loli.net/2024/01/08/NOKYvXAz7tb218i.png)

![2018-07-06-10-26-44](https://s2.loli.net/2024/01/08/21IhpnBut8HWAGo.png)

Android

```
ANDROID_HOME
C:\Users\Administrator\AppData\Local\Android\Sdk
PATH
%ANDROID_HOME%\tools;%ANDROID_HOME%\tools\bin;%ANDROID_HOME%\platform-tools;
```

Gradle

```
GRADLE_HOME
D:\Program Files\Android\Android Studio\gradle\gradle-4.4
PATH
%GRADLE_HOME%\bin;
```

### Genymotion

[Genymotion Desktop](https://www.genymotion.com/desktop/)

个人版

https://www.genymotion.com/fun-zone/

完整版

https://www.genymotion.com/get-full-version/

上面都是下载的同一个安装文件

![2018-07-11-16-20-31](https://s2.loli.net/2024/01/08/JioAtjVNkygaRFM.png)

下载个人版

![2018-07-11-16-28-00](https://s2.loli.net/2024/01/08/WCuVLpgrDFOUE53.png)

前面 virtual box 已经单独安装过了

所以选择不带 virtualbox 的

![2018-07-11-16-33-24](https://s2.loli.net/2024/01/08/qaCjYtoMSvBAnr1.png)

可以 personal use

但是 Add 模拟器的时候需要登陆![2018-07-11-16-36-05](https://s2.loli.net/2024/01/08/mKWc3e7ZagFGpqd.png)



创建完模拟器后可以 退出账号，不影响一般使用

![2018-07-11-16-40-45](https://s2.loli.net/2024/01/08/6DR7aAOYenzmHFf.png)

没有登陆的情况下，选择 Personal use，模拟器底部会又这个水印

登陆的情况下，账号的试用期超过 30 天就用不了吧。

#### 在 Genymotion 中配置使用 Android Studio 的 tools

![2018-07-06-11-00-39](https://s2.loli.net/2024/01/08/afHYZNqBmQKFUGO.png)

#### 在 Android Studio 中配置 Genymotion

安装 Genymotion 的插件

![2018-07-06-10-20-38](https://s2.loli.net/2024/01/08/mAU1jqLhgEDrwzT.png)

指定 Genymotion 的安装目录

![2018-07-06-10-21-22](E:\demo\blog\source\_posts\cordova开发环境搭建\2018-07-06-10-21-22.png)

#### 配置代理

![2018-07-06-10-23-44](https://s2.loli.net/2024/01/08/OjBRycZMAlu5iqT.png)

#### 新建模拟器

![2018-07-06-10-35-47](https://s2.loli.net/2024/01/08/HYukVGnA6JK3yDP.png)



![2018-07-06-10-36-51](https://s2.loli.net/2024/01/08/AULBmcHTYJMFDal.png)

#### 在 Android Studio 中新建项目

新建项目

![2018-07-06-10-39-13](https://s2.loli.net/2024/01/08/vJCDybTpPUF71tm.png)

指定包名

![2018-07-06-10-40-34](https://s2.loli.net/2024/01/08/eoJ4uLBpMIYK75q.png)

目标 API 版本

![2018-07-06-10-41-46](https://s2.loli.net/2024/01/08/VbksDL8UepugK6M.png)

添加一个 Activity

![2018-07-06-10-43-08](https://s2.loli.net/2024/01/08/qhF8M73zwJVfUCA.png)
![2018-07-06-10-43-21](https://s2.loli.net/2024/01/08/uhoxj82aqRW5tzQ.png)

新建项目完成

![2018-07-06-10-45-08](https://s2.loli.net/2024/01/08/wlu9fG3rBc4p8Ot.png)

运行

![2018-07-06-10-45-54](https://s2.loli.net/2024/01/08/V3zxQhH6YPfUs2d.png)
![2018-07-06-10-49-52](https://s2.loli.net/2024/01/08/iMzFCusQaX8lNtK.png)
![2018-07-06-10-50-05](https://s2.loli.net/2024/01/08/3g2OQHVdbZ6Dpxo.png)

修改

![2018-07-06-10-49-31](https://s2.loli.net/2024/01/08/eI3B7RGc2TYJpaC.png)
![2018-07-06-10-51-43](https://s2.loli.net/2024/01/08/AnyVHgmv6QRbtDe.png)
![2018-07-06-10-51-51](https://s2.loli.net/2024/01/08/6rMQXmhRkFNJfxI.png)

### nodejs

https://nodejs.org/zh-cn/

#### 配置国内镜像源

```bash
node -v
npm config -g set registry "https://registry.npm.taobao.org"
npm config list -g
```

![2018-07-06-11-10-58](https://s2.loli.net/2024/01/08/z12lE5VSvJ4Rqte.png)

### Cordova

https://cordova.apache.org/#getstarted

```bash
npm i -g cordova
```

#### 新建项目

```bash
cordova create RunningMan
cd RunningMan
cordova platform add android
cordova platform ls
cordova run android
```

Genymotion 的模拟器正在运行的情况下，Cordova 会把打包好的 apk push 过去

![2018-07-06-14-28-50](https://s2.loli.net/2024/01/08/mH8jvGZDASqnpi6.png)

#### 打包例子 [vue-tetris](https://github.com/Binaryify/vue-tetris/)

把 vue-tetris 项目 clone 到本机，修改配置，安装依赖，build

![2018-07-06-11-52-35](https://s2.loli.net/2024/01/08/BqZPEAQle3DKIM6.png)

修改 `config/index.js => build => assetsPublicPath`

```javascript
assetsPublicPath: './',
```

![2018-07-06-11-49-12](https://s2.loli.net/2024/01/08/uObgAihdLNM4scT.png)

把`npm run build` 生成的 dist 目录下的文件全部复制到 Cordova 项目的 www 文件下
在 Cordova 项目下运行 `cordova run android`
发现 Genymotion 模拟器中运行了，但一直是加载的画面

浏览器中连接 Android
`chrome://inspect/#devices`![2018-07-06-13-37-46](https://s2.loli.net/2024/01/08/rIOivUcMowEnLsJ.png)


console 报错 `navigator.languages.find is not a function`

搜索后定位到 `src/unit/const.js`

![2018-07-06-11-48-14](https://s2.loli.net/2024/01/08/Cq8wiQpFZs4BGfd.png)

修改源码后重新 `npm run build`
把生成的 dist 目录下的文件全部复制到 Cordova 项目的 www 文件下
在 Cordova 项目下运行 `cordova run android`

![2018-07-06-13-39-15](https://s2.loli.net/2024/01/08/pBozc1RVLYKvkng.png)

#### 打包例子 [Vux]("https://doc.vux.li/zh-CN/install/biolerplate.html")

```bash
npm install vue-cli -g # 如果还没安装
vue init airyland/vux2 projectPath

cd projectPath
npm i # 安装依赖 或者 yarn
npm run dev # 或者 yarn dev
npm run build # 或者 yarn build
```

```bash
#  不使用 yarn 可以忽略
npm i -g yarn
yarn config set registry https://registry.npm.taobao.org
```

修改 `config/index.js => build => assetsPublicPath`

```javascript
assetsPublicPath: './',
```

把生成的 dist 目录下的文件全部复制到 Cordova 项目的 www 文件下
在 Cordova 项目下运行 `cordova run android`

![2018-07-06-14-08-30](https://s2.loli.net/2024/01/08/HuMT8YfaLWEorGO.png)

#### Camera

[cordova-plugin-camera](https://cordova.apache.org/docs/en/latest/reference/cordova-plugin-camera/index.html)

```bash
cd cordova8
cordova plugin add cordova-plugin-camera
cordova plugin ls
```

[Cordova 结合 Vue 学习 Camera](https://www.jianshu.com/p/2370548b25ab)

![indexhtmladdcordovajs](https://s2.loli.net/2024/01/08/oQP1UKHWrfI8lGd.png)
![indexhtmladdcordova](https://s2.loli.net/2024/01/08/T6oPp5eACMhOnc1.png)

>     别忘了在cordova项目下添加camera插件
>     vuejs工程build出来的dist/index.html里面加上cordova.js
>     否则调用不到相机插件
>     看看那个cordova framework7 的模板build出来的dist/index.html是不是被模板自动加上了cordova.js

---

### Android APK 手动打包流程

Android app 的打包流程大致分为 **build** ,**sign** , **align** 三部分。

**build** 是构建 APK 的过程，分为 debug 和 release 两种。release 是发布到应用商店的版本。

**sign** 是为 APK 签名。不管是哪一种 APK 都必须经过数字签名后才能安装到设备上，签名需要对应的证书（keystore），大部分情况下 APK 都采用的自签名证书，就是自己生成证书然后给应用签名。

**align** 是压缩和优化的步骤，优化后会减少 app 运行时的内存开销。

Cordova 作为 hybrid app 的框架不像纯 Android 开发那么自动化，所以第一次打 release 包我们需要了解一下手动打包的过程。

#### Build

首先，我们生成一个 release APK 。这点在 cordova build 命令后加一个 --release 参数局可以。如果成功，你可以在 android 的 apk 目录下看到一个 android-release-unsigned.apk 文件。

```bash
cordova build android --release
```

#### Sign

我们需要先生成一个数字签名文件（keystore）。这个文件只需要生成一次。以后每次 sign 都用它。

```bash
keytool -genkey -v -keystore release-key.keystore -alias cordova-demo -keyalg RSA -keysize 2048 -validity 10000
```

上面的命令意思是，生成一个 release-key.keystore 的文件，别名（alias）为 cordova-demo 。
过程中会要求设置 keystore 的密码和 key 的密码。我们分别设置为 testing 和 testing2。这四个属性要记牢，下一步有用。

然后我们就可以用下面的命令对 APK 签名了

```bash
jarsigner -verbose -sigalg SHA1withRSA -digestalg SHA1 -keystore release-key.keystore android-apk/android-release-unsigned.apk cordova-demo
```

这个命令中需要传入证书名 release-key.keystore，要签名的 APK android-release-unsigned.apk，和别名 cordova-demo。签名过程中需要先后输入 keystore 和 key 的密码。命令运行完后，这个 APK 就已经改变了。注意这个过程没有生成新文件。

#### Align

最后我们要用 zipalign 压缩和优化 APK

```bash
zipalign -v 4 android-apk/android-release-unsigned.apk android-apk/cordova-demo.apk
```

这一步会生成最终的 APK，我们把它命名为 cordova-demo.apk 。它就是可以直接上传到应用商店的版本。

### 自动打包

一旦有了 keystore 文件，下次打包就可以很快了。你可以在 cordova build 中指定所有参数来快速打包。这会直接生成一个 android-release.apk 给你。

```bash
cordova build android --release -- --keystore="release-key.keystore" --alias=cordova-demo --storePassword=testing --password=testing2
```

但每次输入命令行参数是很重复的，Cordova 允许我们建立一个 build.json 配置文件来简化操作。文件内容如下

```bash
{
  "android": {
    "release": {
      "keystore": "release-key.keystore",
      "alias": "cordova-demo",
      "storePassword": "testing",
      "password": "testing2"
    }
  }
}
```

下次就可以直接用 cordova build --release 了。

为了安全性考虑，建议不要把密码放在在配置文件或者命令行中，而是手动输入。你可以把密码相关的配置去掉，下次 build 过程中会弹出一个 Java 小窗口，提示你输入密码。

好了，到这里，cordova 的环境配置打包发布一整套流程全部讲完了。
