---
title: 如何在离线环境查看echarts文档
date: 2024-03-07 09:59:09
tags:
    - 离线
    - echarts
---

## 背景
ECharts是一个使用 JavaScript 实现的开源可视化库，涵盖各行业图表，满足各种需求。但是其每个图表的配置项繁杂，开发过程中免不了要经常查看说明文档。

那么如何在离线环境查看echarts文档呢？

<!-- more -->

## 部署方式

1. 下载 [echarts-website](https://github.com/apache/echarts-website "echarts-website") 文件解压并放置在 Nginx服务器中
2. 修改nginx.conf文件如下
```
 location / {
            root   html/echarts-website;
            index  index.html index.htm;
 		   try_files $uri $uri/ /index.html;
       }

	location /echarts-website {
		alias html/echarts-website;
		index  index.html index.htm;
 		try_files $uri $uri/ /index.html;
	}
	```
3. 启动nginx服务，访问```127.0.0.1/echarts-website```即可，得到如下页面即成功
   **注意：**访问路径，其中 echarts-website 是写死的路径。

![image-20240307100038892](https://s2.loli.net/2024/03/07/AwG7BC6KkLZp1ub.png)