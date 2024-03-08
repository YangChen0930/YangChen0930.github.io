---
title: 如何使用echarts和SVG图实现大屏地图
date: 2024-03-08 10:54:13
tags:
    - echarts
    - 大屏
---

## 背景

最近公司有个大屏项目涉及到地图图表，设计图大概如下

![2024-03-08_095145_2744770.8905068757328998.png](https://s2.loli.net/2024/03/08/p5OKUZHlDG9rJtB.png)

正常来说只需要有GeoJSON数据和对应点的经纬度坐标信息Echarts是可以实现这样的效果，但是产品说客户只有矢量图而且也没有坐标信息。

那么就只能用SVG渲染地图，再手动添加一些额外点元素。而Echarts5.1版本后是支持使用SVG作为底图的，那就来试试吧

## 使用散点图

![2024-03-08_095908_2103500.592263247921406.png](https://s2.loli.net/2024/03/08/6t4MBrbkWNLe7vm.png)

通过官方网站的说明和[案例](https://echarts.apache.org/examples/zh/editor.html?c=geo-svg-scatter-simple "案例")可知道我们所需要的应该是在SVG地理坐标系上绘制散点图这个。

## 确实坐标轴

好的，散点图就不多做介绍官方文档都有说明，但是散点是怎么显示在地图上不同位置的，每个点的坐标是怎么得到的这是我们需要解决的。

接着往下看

![2024-03-08_100733_2852340.9016899283899918.png](https://s2.loli.net/2024/03/08/ec5oUKFLCGAtOqx.png)

通过散点悬浮提示我们可以得出一个结论：数组前两位表示坐标信息，第三位才是数值大小。

那么这个坐标是怎么来的呢，是相对于谁的坐标？？？

我们来改动一个点的坐标，全部设为0看看

![2024-03-08_101238_5025170.10718576288278203.png](https://s2.loli.net/2024/03/08/7F1YaDAvxHSmbXM.png)

好家伙，点跑到SVG图的左上角了，如此我们不妨大胆猜测下整个SVG地理坐标系是以SVG图左上角为原点的XY坐标轴。

进一步验证下猜测，将最后一个点的坐标改为SVG图的宽高
![2024-03-08_103554_5825330.01829366806109467.png](https://s2.loli.net/2024/03/08/v64lIGkp1WgYtNH.png)
![2024-03-08_103532_7044140.40548592350811263.png](https://s2.loli.net/2024/03/08/go6TWzsQGRxFDua.png)

可以确定我们的猜测是对的，简单花了个图方便理解
![2024-03-08_102044_2802440.09617745352415863.png](https://s2.loli.net/2024/03/08/8PrCKAOeYz9i6k4.png)

## 获取坐标

现在还剩下如何获取坐标这一问题，实际项目中我们并不知道真实坐标是多少，一个个去试太浪费时间。好在官方提供了API给我们使用

```
myChart.setOption({
    geo: {
        map: 'some_svg'
    }
});
myChart.getZr().on('click', function (params) {
    var pixelPoint = [params.offsetX, params.offsetY];
    var dataPoint = myChart.convertFromPixel({ geoIndex: 0 }, pixelPoint);
    // 在 SVG 上点击时，坐标会被打印。
    // 这些坐标可以在 `series.data` 里使用。
    console.log(dataPoint);
});
```

## 结束

以上只是在SVG地图上绘制散点图的基础调研分析，其他一些定制交互跟普通的echarts地图交互是类型这里就不写了自己实现。



