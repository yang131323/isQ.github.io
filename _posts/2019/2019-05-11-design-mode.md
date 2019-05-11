---
layout:     post
title: 'js常用设计模式'
subtitle: '"之实战"'
date:       2019-05-11 20:05:00
author:     "isQ"
header-img: "img/post/2019/20190511/dandelion.jpg"
catalog: true
mathjax: true
tags:
    - 设计模式
    - 记录
---

> js常用设计模式之实战

### 适配器模式

适配器模式就是充当着一个转换器的作用。举个例子吧，比如我们有一个只有VGA接口的显示器，但是现在我们电脑一般都只支持HDMI接口，这时怎么办？对，就是找一个转换器，将显示器和电脑链接起来。而我们的适配器模式和这个转换器的功能相同。适配器模式前端应该是经常使用的，根据reshful接口风格，传回来的数据字段都是前后端协商的，难免后台有改动的时候，如果这个字段我们在成百上千的地方使用过，难道我们一个一个的改？有一个还不错的方法就是vscode 全局搜索，全局替换，但是万一误伤呢（其他地方用了这个字段，但是并不是代表这个接口返回的数据）？这时候适配器模式就可以派上用场了。在前端接受后端数据的地方写一个适配器，将数据转化之后再分发到各个页面，这样既不影响我们的前端的显示，同时我们的改动也非常小，只要配置一下规则就行了。适配器实现的简单的例子如下：

```javascript
/**
 * 场景：假设后台和前端对接数据，一般来说前端与后台协商的字段不会轻易变动，
 * 但是这只是一种协商而已。因此一旦后台某个字段改变了，刚好前端有几十
 * 甚者上百个地方使用了，如果一个个改。。。。
 * 使用适配器模式将可以化解这种尴尬，只有只考虑一层，多层思想一样
 * @param {Object} obj
 * @returns {Object}
 */
function adapter (obj) {
  const data = obj.data || {
    price: 0,
    num: 0,
    vender: 'changde',
    name: 'chrome',
    id: '68.0.0'
  };
  const rule = obj.rule || {
    vender: 'produce'
  }
  obj = obj.data;
  for (let i in data) {
    if (rule[i]) {
      data[i] = (rule[i] in obj) ? obj[rule[i]] : data[i];
    } else {
      data[i] = (i in obj) ? obj[i] : data[i];
    }
  }
  return data;
}
```

### 装饰器模式

装饰器模式是指在不改动原有功能的基础上添加新功能。说一个实际点的情况吧，你现在有一个当点击某个dom时有一段js动画，但是你不知道这个dom是否已经有同事写过点击事件绑定了，如果已经有同事写过了，那么直接给dom绑定你将覆盖同事的辛辛苦苦写的代码（估计想砍你的心都有了）。关于这个例子装饰器模式示例如下：

```javascript
/**
 * 场景：假设有一个dom当点击它的时候调用某一个函数，现在需求改变（在你写好之后），现在要增加
 * 某个操作。当然只有一个地方使用你可以直接改一改就行，但是情况并不是你想的那样，有上百的dom
 * 都有这个需求，这时候你一个个的改？
 * @param {String} id 要操作的元素id
 * @param {Function} fn 你要加的新功能
 */
function decarator (id, fn) {
  const dom = document.getElementById(id);
  if (typeof dom.onclick === 'function') {
    const temp = dom.onclick;
    dom.onclick = function () {
      temp();
      fn()
    }
  } else {
    dom.onclick = fn;
  }
}
```

### 单例模式

单例模式看名称就知道是什么意思了，如果可以实例化的则只能实例化一次，后面的实例化都是返回的都是第一次实例化的实例。为什么需要这种模式呢？想象一下某个实例我们使用了成百上千次，但是其实并不需要每次都实例化，我们只需要借用一个实例来调用它的方法，但是新手在使用的时候并没有考虑这么多，每次用的时候都实例化一次，这样我们就会占用许多内存，而且都是没什么意义的？因此如果使用单例模式对这个兑现进行封装一下，如果这个对象已经实例化过，那么就返回已经实例化的实例。实例化模式有两种模式，以下是使用IIFE（立即调用函数）实现版本：

```javascript
/**
 * 单例模式（IIFE）
 */
const singleMode = (function () {
  let isInstance = null;
  return function (name, id, age) {
    if (!new.target) { throw new Error('This is a Constructor Function, please use new keyword!'); }
    if (isInstance) { return isInstance; }
    this.name = name;
    this.id = id;
    this.age = age;
    isInstance = this;
  }
}());
```

### 观察者模式

在没有详细研究设计模式之前，听别人说观察者模式就是发布订阅模式，直到自己去研究的时候发现它们确实有渊源，但是并不是一个东西。观察者模式它（Subject）会维护一个依赖列表，当内容更新时会主动通知依赖列表里面的所有Observe。但是发布订阅模式是订阅者会在订阅中心订阅它关心的主题，而发布者会在订阅中心发布相关的主题消息，因此它们之间是有一个中间人（调度中心）。有一篇文章对观察者模式和发布订阅模式讲的很不错，我也是看了这篇文章才有所了解[观察者模式 vs 发布-订阅模式](<https://juejin.im/post/5a14e9edf265da4312808d86>)，一下是观察者模式的一个图解（也是从上面那篇博文偷过来的，发布订阅模式的图解也偷自该博文）：

![Observe](/img/post/2019/20190511/observe.png)

下面是发布订阅模式的图解：

![Publish_Subscribe](/img/post/2019/20190511/publish.png)

从图中可以看出发布订阅模式明显比观察者模式多了一个环节，就是中间那一部分（调度中心），发布者和订阅者都只和调度中心相关。因此可以看出发布订阅模式与观察者模式的区别主要就是观察者模式Subject与Observe直接联系，而且双方各自知道对方的存在，但是发布订阅模式发布者和订阅者都只和调度中心有通信，订阅者和发布者并不清楚对方是否存在。

说完了观察者模式和发布订阅模式的区别现在贴一个简单的观察者模式示例：

```javascript
/**
 * 观察者模式/Subject类，必须使用new关键字进行实例化
 * 观察者模式：维护一个依赖列表，当内容更新时主动通知，依赖列表里面的所有Observe。
 */
function Subject () {
  if (!new.target) { throw new Error('This is a Constructor Function, please use new keyword!'); }
  this.list = [];
}

/**
 * 由于每个Subject都有添加、删除和通知功能，为了节省资源消耗，所有实例共享这些方法。
 * 但是重写prototype会丢失constructor属性，因此需要给constructor重新赋值
 */
Subject.prototype = {
  constructor: Subject,
  add (observe) {
    this.list.push(observe);
  },
  remove (observe) {
    let isAdd = this.list.indexOf(observe);
    if (isAdd === -1) { throw new Error('you not have add Observe!'); }
    this.list.splice(isAdd, 1);
  },
  notify (notice) {
    let len = this.list.length;
    for (let i = 0; i < len; i++) {
      this.list[i].update(notice);
    }
  }
}

/**
 * 观察者模式/Observe类，必须使用new关键字进行实例化
 * @param {String} name Observe名字
 */
function Observe (name) {
  if (!new.target) { throw new Error('This is a Constructor Function, please use new keyword!'); }
  this.name = name;
}

/**
 * 由于每个Observe都有update消息功能，为了节省资源消耗，所有实例共享这个方法。
 * 但是重写prototype会丢失constructor属性，因此需要给constructor重新赋值
 */
Observe.prototype = {
  constructor: Observe,
  update (notice) {
    console.log('My name is ' + this.name + ', I attention "' + notice + '" ready update!');
  }
}
```

### 发布订阅模式

发布订阅模式在观察者模式上已经进行了解释，在这里就不进行说明了。其实发布订阅模式我觉得给它写成三部分是最好，一个发布者部分、一个订阅者部分和一个消息调度中心（当然你也可以只用一个部分实现，网上已经有了实现版本）；由于本人有点小懒，使以只写了两部分，发布者部分和调度中心共用了，其实主要是因为这个示例太简单了没办法拆开了，但我个人偏向分三部分写，因为这样既清晰扩展的时候只要改动相应部分就可以了，以下是发布订阅模式的示例（使用了单例模式的另一种实现，class关键词）：

```javascript
/**
 * 发布-订阅模式
 * 发布订阅模式：与观察者的区别在于发布订阅不直接与发布者关联，它有一个调度中心
 * 订阅者只管在调度中心订阅就行，发布者只需要在调度中心发布通知。
 */
const PublishSubscribe = new class {
  constructor () {
    this.subscribeCenter = {}
  }
  publish (event, mes) {
    if (!(event in this.subscribeCenter)) {
      alert('not subscriber!');
      return;
    }
    const subscribes = this.subscribeCenter[event];
    const len = subscribes.length;
    for (let i = 0; i < len; i++) {
      subscribes[i].notice(mes);
    }
  }
  subscribe (event, sub) {
    if (!this.subscribeCenter[event]) { this.subscribeCenter[event] = []; }
    this.subscribeCenter[event].push(sub);
  }
  unSubscribe (event, sub) {
    const haveEvent = event in this.subscribeCenter;
    const isHave = this.subscribeCenter[event].indexOf(sub)
    if (!haveEvent || !~isHave) {
      alert('you not subscribe: ' + event);
      return;
    }
    this.subscribeCenter[event].splice(isHave, 1);
  }
}

/**
 * 订阅者类，必须使用new关键字进行实例化
 * @param {String} name Subscribe名字
 */
function Subscribe (name) {
  if (!new.target) { throw new Error('This is a Constructor Function, please use new keyword!'); }
  this.name = name;
}

/**
 * 由于每个订阅者都有notice消息功能，为了节省资源消耗，所有实例共享这个方法。
 * 但是重写prototype会丢失constructor属性，因此需要给constructor重新赋值
 */
Subscribe.prototype = {
  constructor: Subscribe,
  notice (mes) {
    console.log('My name is ' + this.name + ', I attention "' + mes + '" ready update!');
  }
}
```

到此常用的几种设计模式已经讲完了。