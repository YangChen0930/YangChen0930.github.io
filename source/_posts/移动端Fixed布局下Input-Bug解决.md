---
title: 移动端Fixed布局下Input-Bug解决
date: 2019-07-23 10:53:52
tags:
  - ios
  - bug

categories: 
    - 技术
---

在移动端开发中，fixed 元素和 input 输入框在同一页面是常有的事。但是 在键盘配唤起
（也就是输入框获取焦点）的情况下，会出现一些莫名其妙的 Bug，尤其在 ios 中。这里记录下我遇到的一些问题以及解决方案。

# ios 下的 Fixed 和 Input Bug

假设有这样一个页面：

```
  <body>
    <header>
      ...
    </header>

    <main>
      ...
    </main>

    <footer>
      <input type="text" value="">
      <button class="btn"></button>
    </footer>
  </body>
```

对应的页面样式如下:

```
  header{
    position: fixed;
    height: 44px;
    left:0;
    right:0;
    top:0;
  }
  footer {
    position: fixed;
    height: 40px;
    left: 0;
    right:0;
    bottom:0;
  }
  main{
    min-height: 100%;
    margin-top:44px;
    margin-bottom: 40px;
  }
```

这样一个常规的 fixed 布局就完成了，目前事完全正常的。

但是底部输入框唤起键盘以后，再次滑动页面，就会发现 fixed 布局元素跟随页面滚动起来了？？？fixed 属性失效了！！！

怎么回事啊，老弟？？？

<!-- more -->

原来是在键盘被唤起后，fixed 元素失效，换句话说就是元素不能浮动，与 absolute 一致，而当页面高度超出一屏时，失效的 fixed 元素会随页面滚动一起滚动。

既然如此，那让页面整体高度固定且不能滚动，就算 fixed 属性还是失效，也不就会出现上面的问题了。

OK，照着这个方法，fixed 元素的父级不能滚动，就要将原本的滚动区域移到一个固定高度的元素里，还按照上面那个布局来看，就是将滚动内容移到 main 内部，再给 main 一个定高，代码如下:

```
<body>
    <header>
      ...
    </header>

    <main>
      <div class="contene">
        ...
      </div>
    </main>

    <footer>
      <input type="text" value="">
      <button class="btn"></button>
    </footer>
  </body>
```

```
  其他布局不变

  main {
    position: absolute;
    top: 44px;
    bottom: 40px;
    overflow-y: scroll
  }
```

看上去时解决问题了，在手机上实际测试一下，也是没问题的，但是发现滚动十分不流畅，手指
松开后，滚动立即停止。加上下面的属性就可以恢复弹性滚动。

```
  -webkit-overflow-scrolling: touch
```

再来看下，嗯~ o(_￣ ▽ ￣_)o，立刻纵享丝滑。

好的，到此为止，fixed 布局在 ios 中的 bug 总算解决了。当然，还可以借助 isScroll.js 解决这个问题。

此外，在 ios 中使用第三方输入法，输入法在被唤起时经常盖住输入框，只有在输入文字
后，输入框才会浮出。目前没有发现什么好方法可以解决这个问题，算是 ios 的一个坑。

# 混合 App 下的布局

在 app 里唤起键盘后，键盘同样也占据一定的屏幕高度，结果就会导致整个页面的高度被挤压。由于 fixed 元素是相对于 body 定位的，这样会出现 fixed 元素盖住输入框的情况（在一些小屏手机上更严重）。

例如下面这个示例:

```
  <body class="layou-fixed>
    <form class="form">
      <input type="text">
    </form>
    <button class="btn">提交</button>
  </body>
```

```
  .layout-fixed {
    ...
  }

  .form {
    ...
  }

  .btn {
    position: fixed;
    bottom: 30px;
    ...
  }
```

编译打包成 App，在手机上测试一下，就出现上述问题。

由于是键盘导致的页面高度不够，所以尽量不要用 fixed 布局。这里可以使用 css 最新的 flex 布局，完美的解决了页面高度不够的问题。

```
  .layout-fixed{
    display: flex;
    flex-direction: column;
    justify-content: center;
  }
  .form{
    flex: 1;
  }
  .btn{
    margin-bottom: 30px;
  }
```

这样当键盘唤起造成页面高度不够时，页面会变成可滚动的，而整体布局不会变化，避开了 fixed 布局的 bug。

# 其他

有时候输入框获取焦点(focus)后，会出现键盘盖住输入框的情况，这时候可以尝试 input 元素的 scrolltoView 进行修复。

页面滚动到上下边缘的时候，一旦继续拖拽会将整个 view 一起拖拽走，导致页面露底。

为了防止页面露底，可以在页面拖拽到页面边缘的时候，通过判断拖拽方向以及是否为边缘来阻止 touchmove 事件，防止页面继续拖拽。

以上面 ios 下的 fixed 布局为例，一下代码仅供参考:

```
  // 防止内容区域滚到底后引起页面整体的滚动
  var content = document.querySelector('main');
  var startY;

  content.addEventListener('touchstart', function (e) {
      startY = e.touches[0].clientY;
  });

  content.addEventListener('touchmove', function (e) {
      // 高位表示向上滚动
      // 底位表示向下滚动
      // 1容许 0禁止
      var status = '11';
      var ele = this;

      var currentY = e.touches[0].clientY;

      if (ele.scrollTop === 0) {
          // 如果内容小于容器则同时禁止上下滚动
          status = ele.offsetHeight >= ele.scrollHeight ? '00' : '01';
      } else if (ele.scrollTop + ele.offsetHeight >= ele.scrollHeight) {
          // 已经滚到底部了只能向上滚动
          status = '10';
      }

      if (status != '11') {
          // 判断当前的滚动方向
          var direction = currentY - startY > 0 ? '10' : '01';
          // 操作方向和当前允许状态求与运算，运算结果为0，就说明不允许该方向滚动，则禁止默认事件，阻止滚动
          if (!(parseInt(status, 2) & parseInt(direction, 2))) {
              stopEvent(e);
          }
      }
  });
```

# 参考

[web 移动端 Fixed 布局解决 iOS 下的 Fixed+Input BUG 现象]("http://menvscode.com/detail/597ee129549d941107716d14")
