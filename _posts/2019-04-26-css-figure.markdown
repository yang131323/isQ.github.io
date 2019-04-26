---
layout:     post
title:      "使用CSS生成常见图形"
subtitle:   " \"之实现原理\""
date:       2019-04-26 20:45:00
author:     "isQ"
header-img: "img/post/20190426/sunflower.jpg"
catalog: true
mathjax: true
tags:
    - CSS
    - 记录
---

> css面试题之生成各种常见图形

### 前言

最近一直在看面试题，发现了一些有趣的东西，有很多东西以前不是很懂，都停留在使用的层次上，所以现在开始恶补基础。刚好了看到了一个面试题，感觉以前没有对这个知识点了解的深入，因此现在做个笔记，防止自己忘记。

### css图形实现原理

如果你是一名前端开发人员，那么你一定熟悉css吧！那你试一下用纯css实现一个三角形、五角星、平行四边形？用css生成的各种图形基本上都依赖于border、border-radius属性。这里不记录具体怎么用css实现一个图形，只说明如何利用这两个属性，当然除了这两个属性之外，复杂的图形还需要用before、after这两个强大的伪类。

在解释如何用border生成图形之前先来加强一个认识，先声明一个div，然后设置border效果如下：

![border样式](/img/post/20190426/border-one.png)

为什么border是梯形呢？详解如下图：

![border详情](/img/post/20190426/border-detail.png)

border的上边宽由content宽度决定（就是图片的内容width）下边框等于content宽度+左右两边border宽度，高度等于我们设置的border大小，而且边框相接的地方是各自一半；因此要用css形成一个三角形就很简单了，三角形只有三边，那么就设置内容width等于0，这样就达到了三边的目的了。但是宽度呢？由于border下边宽的高度由三部分组成：内容宽度、左、右border宽度。因此宽度可以通过设置border-left、border-right来完成，高度就直接设置border大小就可以了。形成过程如下图：

![三角形生成原理](/img/post/20190426/triangle.png)

根据该推理得出的代码如下，可以将一下代码在自己本地编辑器进行验证：

```
.tranigle {
  height: 0;
  width: 0;
  border-bottom: 100px solid yellowgreen;
  border-left: 50px solid transparent;
  border-right: 50px solid transparent;
}
```

这只是其中的一种，根据上面的原理，还可以实现直角三角形（border-left和border-right设置一个就行）、等腰直角三角形等等。

理解了上面的实现原理几乎就可以实现很多图像了，比如椭圆（利用border-radius可以设置两个半径）、五角星（使用一个三角形加上before、after拼接成另三个角）、六角形（两个三角形）、八角形（两个四边形，旋转一定角度transform: rotate(45deg)）、十二角形（三个四边形，旋转一定角度）、吃豆人（就是一个圆，隐藏右边的90度）、平行四边形（矩形，变形skew）等等。这里有一篇文章实现了大部分图形[45个值得收藏的 CSS 形状](<https://juejin.im/post/5cbd1f0ae51d456e5e035f45?utm_source=gold_browser_extension#heading-24>)。