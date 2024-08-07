---
title: Hexo-Github-page搭建博客
date: 2019-07-22 13:53:06
tags:
  - Hexo
  - Github
  - Blog

categories:
    - 学习笔记
---

# 前言

前段时间对个人博客很感兴趣，而我这人又有点心血来潮，想干什么就一点要做，于是决定自己动手丰衣足食，顺便整理下，方便以后查看。

博客有第三方平台，也可以自建，比较早的博客园、CSDN，近几年新兴的也比较多诸如：WordPress、segmentFault、简书、掘金、知乎专栏、Github Page 等等。

综合考虑成本和个性化两方面，我采用了 Githb Page + Hexo 搭建个人博客的方式。Hexo 是使用 nodejs 编写的一个静态博客生成工具，而 Github Page 是 Github 提供的一种免费的静态网页托管服务，支持 Jekyll、Hugo、Hexo 编译静态资源。

Let's Go ~

# 准备环境

准备 node 和 git 环境，
首先，安装 [node.js]("https://nodejs.org/en/")，因为 Hexo 是基于 node.js 服务的博客框架。

后然安装[git]("https://git-scm.com/")，一个免费开源的分布式版本控制系统，具体内容网上有详细介绍，这里就不过多说明。

安装成功后打开命令行终端，在终端中输入以下命令验证是否安装成功。

```
  node -v
  npm -v
  git version
```

<!-- more -->

# 安装 Hexo

node 和 git 安装成功后就可以安装 Hexo。
在命令行输入执行以下命令:

```
  npm install -g hexo-cli
```

安装完成后，再次执行下列命令，查看是否安装成功。

```
  hexo version
```

下面开始创建博客

```
  hexo init blog
  cd blog
  npm install
```

创建完成，文件夹目录如下：

```
  .
  ├── _config.yml # 网站的配置信息，您可以在此配置大部分的参数。
  ├── package.json
  ├── scaffolds # 模版文件夹
  ├── source  # 资源文件夹，除 _posts 文件，其他以下划线_开头的文件或者文件夹不会被编译打包到public文件夹
  |   └── _posts # 文章Markdowm文件
  └── themes  # 主题文件夹
```

如果之前的命令都没有报错的话，那么恭喜，博客创建成功啦！！！运行 hexo server，也可以直接运行 hexo s，没有报错的话，在浏览器中输入 http://localhost:4000 回车就可以看到预览效果了。

```
  hexo server
```

OK，你的本地博客已经搭建成功，下面就是部署到 Github Page 了。

# 创建 Github page

注册登录略过，不会自行百度。

点击 Start project 或者下面的 new repository 创建一个新的仓库

**_注意：Github 仅能使用一个同名仓库的代码托管一个静态站点。_**

**这里有个硬性规定就是仓库名一定是 用户名.github.io，比如我的用户名为 YangChen0930，那么仓库名就是 YangChen0930.github.io。**

创建完成后，点击 Settings 设置远程仓库，找到 Github Page， 可以看到 Github Pages 已经被启用，同时资源正在从 **master** 分支构建。

输入 用户名.github.io 地址测试 Github page 是否创建成功。当然目前页面是没有内容的。

下面要做的就是同步本地创建的博客到 github 仓库

# 部署到 Github

可以直接查看官网的[部署]("https://hexo.io/zh-cn/docs/deployment")教程

第一步:修改项目根目录下的\_config.yml 配置文件配置参数。

```
  deploy:
  - type: git
    repo: url
    branch: master (默认)
```

也可同时部署到多个仓库

```
  deploy:
  - type: git
    repo:
  - type: heroku
    repo:
```

第二步: 安装部署插件 hexo-deployer-git

```
  npm install hexo-deployer-git --save
```

最后生成文件部署上传

```
  hexo generate # 生成静态文件
  hexo deploy # 部署
  或
  hexo generate --deploy # 简写 hexo g -d
```

上传完成， 在浏览器访问：http://用户名.github.io 就可以看到自己的博客了。

# 新增博客

博客搭好了，目前只有一篇默认的 hello-world 文章，现在开始写我们自己的文章，这里简单介绍下流程，具体文档可以看 hexo 官网。

```
hexo new '文章标题'
```

执行完成后可以在/source/\_posts 下看到一个新增的 "文章标题.md" 的文件。.md 是 Markdown 格式的文件，语法比较简单，可以到网上找找。

在 Markdown 文件里编写自己的文章内容，保存。

再执行一下下面的命令

```
  hexo generate
```

```
  hexo server
```

就可以在本地预览新增的博客了。

最后，只要同步部署到 Github 上就行了

```
  hexo clean
```

```
  hexo generate --deploy
```

部署前应该先执行 hexo clean 命令，清除缓存文件和已生成的静态文件。如果出现更改内容（尤其是换主题后）无论怎样都不生效时，可能就是没有运行该命令。

此外，还可以通过 hexo new draf "文章标题" 生成草稿文件，生成后会在/source/\_drafs 里看到新增的草稿文件。可通过 `publish`命令将草稿移动到/source/\_post 文件夹。

```
  hexo publish [layout] <title>
```

# 切换主题

在 hexo 官网或则网上都可以找自己喜欢的主题，我现在使用的是 next 主题。一般主题都有使用文档，你可以根据说明文档修改相应的配置。
