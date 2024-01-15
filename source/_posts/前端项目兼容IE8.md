---
title: 前端项目兼容IE8
date: 2022-05-08 14:42:58
tags:
    - IE
---


## 背景

南天门（盘库）项目招标文档明确需求要支持ie8。

## 现状

但是目前三大主流框架都不支持ie8

- #### vue

vue从最初就不支持ie8，最低支持ie9，因为vue的核心数据劫持使用了ES5的特性Object.definedProprty来实现，而特性的浏览器兼容最低是ie9，所以vue从根本是不支持ie8。

- #### react

![null](https://s2.loli.net/2024/01/08/sG5mPRyxoFlnXZd.png)

官方也从2016年开始宣布不支持ie8，官方的建议是采用低版本0.14来支持ie8，但我们并不确定哪些特性与功能是可用哪些是不可用的，需要花时间去验证，是否值得？？？

- #### angular

angularjs1.3已经抛弃了对ie8的支持

![null](https://s2.loli.net/2024/01/08/YCVNOoplH2kXRvf.png)

## 结论

 主流框架都已不支持ie8，只能另寻他路（要么使用bootstrap之类原生的框架，要么使用一些小众框架）

以下是我找到的支持IE8的框架

https://github.com/RubyLouvre/anu 司徒正美（作者已去世）

一个迷你的类似react的框架，与React16非常兼容，兼容ie6-ie8

https://github.com/RubyLouvre/avalon 司徒正美（作者已去世，目前没人维护）

MVVM框架，跟vue用了同样的数据劫持方式，不过对不支持Object.defineProperty的浏览器使用VBScript来实现相同的功能，所以支持ie8

https://baidu.github.io/san/tutorial/start/

San，是一个 MVVM 的组件框架。它体积小巧（< 17K），兼容性好（IE6），性能卓越，是一个可靠、可依赖的实现响应式用户界面的解决方案。

https://github.com/yoxjs/yox

就像Vue一样，但它比Vue更轻，更容易使用。

提供两个大版本，如下：

- **standard**: 适合标准浏览器和 NodeJS
- **legacy**: 适合低端浏览器（IE6、IE7、IE8)
