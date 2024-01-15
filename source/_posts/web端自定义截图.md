---
title: web端自定义截图
date: 2023-07-14 14:37:13
tags:
    - web
---

## 写在前面

首先浏览器并没有提供一个类似的API权限功能，所以只能某种方式来实现一个伪截图功能。

## 功能分析

简单分析一些截图软件可以得到了一下实现思路：

- 获取当前可视区域的内容，将其存储起来
- 为整个可视区域绘制蒙层 
- 在获取到的内容中进行拖拽，绘制镂空选区
- 选择截图工具栏的工具，选择确认取消等信息
- 在选区内拖拽绘制对应的图形
- 将选区内的内容转换为图片

不难发现，难点就在于获取当前可视区域内容。

首先想到的技术就是canvas。将body文档内容绘制到canvas，然后利用canvas的`toDataUrl()`等方法即可获取到图片。

正好有个[html2canvas](https://html2canvas.hertzen.com/)开源库可以实现将指定dom转换为canvas。关于canvas截图实现具体可以参考之前的一篇[调研文档](2023/01/09/网页截图功能调研/)

## 使用webrtc截取整个屏幕

上述canvas截图，因为html2canvas要遍历整个body中的dom，然后再转换成canvas，而且图片还不能跨域，如果页面中图片一多，它会变得非常慢。同时由于它是自己实现的一套css逻辑所以部分css样式不支持或者不完整。 [常见问题](https://html2canvas.hertzen.com/faq)

![null](https://s2.loli.net/2024/01/08/U5y2Vbz9IY3AWri.png)

基于上述原因，因而有了现在这种方案 —— 使用浏览器原生API webERT

具体思路如下：

- 使用getDisplayMedia来捕获屏幕以录屏方式从其中拿出一帧，得到MediaStream流
- 将得到的MediaStream流输出到video标签中
- 使用canvas将video标签中的内容绘制到canvas容器中

它截取出来的东西更精确、性能更好，不存在卡顿问题也不存在css问题，但是需要用户授权和做一些窗口选择，相比于html2canvas方案做不到默认截图。

> 使用webtrc技术，需要你的网站运行在https环境或者localhost环境。当然，也可以通过修改浏览器设置的方式实现在所有环境下都能运行。步骤如下： 1.打开谷歌浏览器，在地址栏输入chrome://flags/#unsafely-treat-insecure-origin-as-secure 2.在打开的界面中：下拉框选择enabled，地址填写你的项目访问路径

## 结尾

好了，有关web端截图的两种方案都介绍完了，可以说是可有优劣。具体使用那个要看具体需求进行一定取舍。

## 两种方案实际效果对比

使用一下页面进行试验

![null](https://s2.loli.net/2024/01/08/rg4VWOdvMkwX739.png)

- html2canvas

![null](https://s2.loli.net/2024/01/08/3WvbLUQ1Anu95IM.png)

- webrtc

首先需要权限确认！！！

![null](https://s2.loli.net/2024/01/08/akO7wEGTeducWFH.png)

![null](https://s2.loli.net/2024/01/08/UgcX7COxLiWQ9qA.png)
