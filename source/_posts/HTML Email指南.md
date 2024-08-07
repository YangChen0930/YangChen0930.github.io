---
title: Email指南
date: 2023-03-03 14:09:45
tags:
    - web
    - email
categories:
    - 规范
---

如何发送一个html格式的邮件给用户呢？

1.手动编写html格式的邮件，几乎所有邮箱都支持，这里以阿里邮箱举例，点击源码后就可以直接编写html，然后发送

![null](https://s2.loli.net/2024/01/08/HqQbn19Fxl25NTJ.png)![null](https://s2.loli.net/2024/01/08/D9S1odvlCApcP28.png)

![null](https://s2.loli.net/2024/01/08/KAUL6Zv1OMCWiTG.png)

这种方式只适用于单个内容发送。而批量动态的发送就需要用到第二种方式

<!-- more -->

2.自动发送

前端提供一个或多个模板给后端，后端使用发送服务自动发送html邮件给用户

好了，两种方式的细节我们不去深究，把目光放到html模板和手动编写html上来。

那么，我们是不是可以直接正常的编写html文档作为模板呢？答案是否定的，各个邮箱对html支持程度各不相同，同样一份html文档在某些邮箱里正常显示而在某些邮箱却只能展示部分。所以为了兼容性，我们需要遵守一些基本规范。具体可参考一下说明。

[链接](http://www.ruanyifeng.com/blog/2013/06/html_email.html)

[链接](https://www.cnblogs.com/yjzhu/archive/2012/11/05/2755155.html)

注：由于不支持js和script标签，所以那怕基本的交互都会失效

基于这些准则，我们可以编写一个兼容绝大数邮箱的html邮件。

也可以使用一些现成的框架和模板，比如[mjml](https://mjml.io/) 和 [emailiframe](https://emailframe.work/)

使用阿里、腾讯、网易邮箱分别对mjml框架进行测试，主要试验了手风琴和图片轮播器两个交互，支持度如下

| 框架 | 支持度                                                       | 说明                                               |
| ---- | ------------------------------------------------------------ | -------------------------------------------------- |
| 阿里 | 低![null](https://s2.loli.net/2024/01/08/ARlS7fEG9srIdzX.png) | 完全不支持交互，甚至由于交互丢失有部分内容无法展示 |
| 腾讯 | 中![null](https://s2.loli.net/2024/01/08/up1s4Teo6OHKzbt.png) | 支持部分交互，但是显示不友好                       |
| 网易 | 高![null](https://s2.loli.net/2024/01/08/5L9VIUPqkhrTJAb.png) | 基本支持                                           |

由此判定那怕使用现有框架也无法做到完全兼容交互功能，所以html邮件推荐不是使用交互，全部交互都走链接跳转。
