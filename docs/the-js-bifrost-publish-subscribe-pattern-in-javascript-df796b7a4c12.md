# JS Bifrost——发布-订阅编码方式

> 原文：<https://medium.com/globant/the-js-bifrost-publish-subscribe-pattern-in-javascript-df796b7a4c12?source=collection_archive---------1----------------------->

## JavaScript 设计模式通过将设计模式应用于语言来编写结构化和可维护的 JavaScript。

欢迎回到“JS Bifrost ”,这是您通向神级 JavaScript 的坚实基础的道路。这是本系列的下一篇文章。我们这次的重点是— JavaScript 设计模式 *'* ***发布-订阅'*** 。

![](img/c4017802f2f35fdecb28823d3f579081.png)

如果您想保持代码高效、更易管理，并且与最新的最佳实践保持同步，设计模式将有助于实现这一目标。设计模式提供了经过测试和验证的开发范例，可以加速开发过程，还可以提高代码的可读性。

基于设计模式解决的问题类型，设计模式被分为三个子类别，即创造模式、结构模式和行为模式。在本文中，我们将介绍发布-订阅模式，它是行为模式的一部分。通常被称为**观察者**。

# 发布-订阅模式

在软件架构中，发布-订阅是一种消息传递模式，发布者是消息的发送者，订阅者通常只接收发布的全部消息的一个子集。*发布者和订阅者是松散耦合的。*

![](img/6baba3bfbed8b6da046685c0aa3987a8.png)

**发布-订阅模式在起作用**

创建`pubsub-design-pattern`文件夹和文件`pubsub.js`添加以下代码:

```
let subscribers = {};module.exports = {
    publish() {
        // method to publish an update
    },
    subscribe() {
        // method to subscribe to an update
    }
};
```

一个对象`subscribers`，将跟踪注册用户的回调。在这个对象中，我们将最终存储事件(键/值)对。每个事件都有一个对应于事件名称的键和一个设置为数组的值。在同一个数组中，我们将注册/存储订户回调。只要事件被触发，回调就会被调用。对于任何给定的事件，我们可能会触发几个这样的回调。`pubsub`模块有两个主要功能，一个是“发布”更新，另一个是“订阅”更新。

这是我们`publisher/subscriber`模块的核心。

首先关注用于注册订户回调的 subscribe 方法。它将接受两个参数。第一个是事件名称，第二个是发布事件时要调用的回调。

```
subscribe(event, callback) {
    if (!subscribers[event]) {
        subscribers[event] = [];
    }    subscribers[event].push(callback);
}
```

在`subscribe`函数中，我们首先将检查事件是否已经在`subscribers`对象中注册。如果事件在`subscribers`对象中不存在，我们使用事件名作为关键字注册它，并将值初始化为一个空数组。然后我们将订户回调推入事件数组。

现在，让我们看看发布方法:

```
publish(event, data) {
    if (!subscribers[event]) return;    subscribers[event].forEach(subscriberCallback =>
        subscriberCallback(data));
}
```

在`publish`函数中，我们首先将检查是否有任何订户注册了该事件。如果订户不在，我们可以直接返回。如果订阅者在那里，迭代事件数组并调用每个已经推入事件数组的订阅者回调。这里的数据是可选参数，可以提供也可以不提供。

![](img/2453d21c31f069689fcbb5c4ab1153da.png)

接下来，我们可以创建模块来消费`pubsub`对象。创建名为`publisher.js`和`subscriber.js`的新文件。

在本例中，`publisher`将是发布者，`subscriber`将是订阅者。正如我们将看到的，两个模块将仅通过`pubsub`模块进行通信。

`publisher.js`

```
const pubSub = require("./pubsub");module.exports = {
    publishEvent() {
        const data = {
            msg: "TOP SECRET DATA"
        };

        pubSub.publish("anEvent", data);
    }
};
```

这里，我们导入/需要`pubSub`模块，并导出一个带有对象的`publishEvent`方法。该方法调用`pubSub’s`的发布方法。

`subscriber.js`

```
const pubSub = require("./pubsub");pubSub.subscribe("anEvent", data => {
    console.log(
        `"anEvent", was published with this data: "${data.msg}"`
    );
});
```

这里，我们再次导入/要求 pubSub 模块并调用 subscribe 方法来订阅事件。向该方法传递我们希望订阅的事件名称和一个订阅者回调。

我们的参赛文件准备好了。现在在同一个文件夹中包含一个`index.js`文件。

```
const publisher = require("./publisher");
const subscriber = require("./subscriber");// We use publisher's publishEvent() method
publisher.publishEvent();
publisher.publishEvent();
```

现在，在您的终端中，导航到`pubsub-design-pattern`文件夹并键入:

```
$ node ./index.js
```

它会将以下内容打印到控制台:

```
"anEvent", was published with this data: "TOP SECRET DATA"
"anEvent", was published with this data: "TOP SECRET DATA"
```

目前，在我们完全实现的 **PubSub** 模式中，唯一缺少的是订阅者取消订阅更新的方式。

取消订阅的最好方法是从为模块订阅事件的函数调用中返回一个带有`unsubscribe`方法的对象。为此，重构`pubsub.js`文件中的`subscribe`方法。

```
subscribe(event, callback) {
    let index;        if (!subscribers[event]) {
        subscribers[event] = [];
    }    index = subscribers[event].push(callback) - 1;

    return {
        unsubscribe() {
            subscribers[event].splice(index, 1);
        }
    };
}
```

作为回报，接受`subscribers[event]`数组的`unsubscribe`方法也使用 Javascript 的`splice`方法移除指定索引位置的回调。

在我们的订户中使用这个`unsubscribe`方法。

```
const pubSub = require("./pubsub");
let subscription;subscription = pubSub.subscribe("anEvent", data => {
    console.log(
        `"anEvent", was published with this data: "${data.msg}"`
    );
    subscription.unsubscribe();
});
```

现在再次运行该程序，您将只看到一条记录到控制台的消息。这是因为`unsubscribe`方法。

![](img/de7b3b06944252d944b2e87cabd1e709.png)

# 减去

简而言之，我们创建了一个 pubsub 模块，它确保通过 subscribe 和 publish 方法订阅和发布更新事件。我们有一个名为“events”的命名通信通道，每个事件都跟踪订户回调，这些回调在事件被触发/发布时被调用。我们有一个`unsubscribe()`方法来移除指定索引位置的回调，这允许代码在不再需要更新时自行清理。

发布-订阅模式的缺点是其主要优点的副作用:发布者与订阅者的分离。因此，我们需要小心不要过度使用这种模式，因为它会导致代码晦涩难懂，难以维护。

荣誉🎉您在通往上帝级别的 JavaScript 的 Bifrost 中前进了一步。

敬请期待下一集***JS 彩虹桥*。**

[](/globant/the-js-bifrost-keep-the-this-at-peace-d87011b5a685) [## JS 彩虹桥——让“这个”保持平静！

### 揭开这个令人困惑的关键字和 call、apply、bind 方法用法的神秘面纱。

medium.com](/globant/the-js-bifrost-keep-the-this-at-peace-d87011b5a685) [](/globant/the-js-bifrost-currying-functions-in-javascript-e03a216b4b59) [## JS Bifrost——Javascript 中的 Currying 函数

### JS 的世界

medium.com](/globant/the-js-bifrost-currying-functions-in-javascript-e03a216b4b59) [](/globant/the-js-bifrost-shallow-or-deep-copy-22144e6787d6) [## JS 彩虹糖——浅拷贝还是深拷贝？

### 复制数据都是关于值、引用和内存分配的

medium.com](/globant/the-js-bifrost-shallow-or-deep-copy-22144e6787d6) [](/globant/the-js-bifrost-understanding-the-coding-pattern-called-iife-794b46006550) [## JS 彩虹桥——理解称为(IIFE)的编码模式

### 最受欢迎的函数表达式习语

medium.com](/globant/the-js-bifrost-understanding-the-coding-pattern-called-iife-794b46006550) [](/globant/the-js-bifrost-memoization-it-is-65f890f14308) [## JS 彩虹糖——就是它了！

### 使迭代或递归函数更加优化的编程实践

medium.com](/globant/the-js-bifrost-memoization-it-is-65f890f14308) [](/globant/the-js-bifrost-incredible-javascript-features-587b78865e67) [## JS Bifrost——不可思议的 JavaScript 特性

### 您应该在项目中开始使用的 7 个 Javascript 特性

medium.com](/globant/the-js-bifrost-incredible-javascript-features-587b78865e67) [](/globant/the-js-bifrost-callback-hell-4c699e1954b8) [## JS 彩虹桥——回调地狱

### 你需要知道如何应对这个地狱，如何超越陈词滥调！

medium.com](/globant/the-js-bifrost-callback-hell-4c699e1954b8) [](/globant/the-js-bifrost-all-that-we-need-to-know-about-promises-2c7b087b56f3) [## JS 彩虹桥——关于承诺，我们需要知道的一切

### 理解承诺及其解决实际应用问题陈述的方法

medium.com](/globant/the-js-bifrost-all-that-we-need-to-know-about-promises-2c7b087b56f3) [](/globant/the-js-bifrost-inheritance-in-js-prototype-and-class-inheritance-84ec4c60b1a2) [## JS 中的继承——原型和类继承

### 欢迎来到 JS Bifrost，这是您通向神级 JavaScript 坚实基础的道路。这是下一篇文章…

medium.com](/globant/the-js-bifrost-inheritance-in-js-prototype-and-class-inheritance-84ec4c60b1a2) [](/globant/cleaner-code-with-javascript-functions-d08d3bb37836) [## 带有 JavaScript 函数的更干净的代码

### 了解纯函数和高阶函数来编写最先进的代码！

medium.com](/globant/cleaner-code-with-javascript-functions-d08d3bb37836) [](/globant/the-js-bifrost-nullish-coalescing-operator-6ac55e59f61f) [## JS 双花聚结(？？)运算符

### what-why-how 无效合并运算符以及链接和逻辑运算

medium.com](/globant/the-js-bifrost-nullish-coalescing-operator-6ac55e59f61f)