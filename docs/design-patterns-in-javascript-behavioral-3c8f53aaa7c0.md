# JavaScript 中的设计模式:行为

> 原文：<https://medium.com/globant/design-patterns-in-javascript-behavioral-3c8f53aaa7c0?source=collection_archive---------0----------------------->

![](img/773e15823238b16384b423e44a5b6433.png)

Photo by [George Pagan III](https://unsplash.com/@gpthree?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/patterns?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText).

设计模式是将众所周知且经过测试的解决方案应用于软件开发中常见问题的好方法。*行为*模式是一个设计模式类别，被授予与*沟通和对象之间的分配*情况相关的常见问题。我写这篇文章的目的是为开发者提供一个简单易用的快速参考指南。

# 责任链。

责任链模式允许沿着有机会处理请求的对象链传递请求。收到请求后，每个处理程序决定是处理请求还是将请求传递给链中的下一个处理程序。

在这个例子中，我们链接了折扣类来处理购物车中的折扣是多少的请求。

chain of responsability

当不止一个对象可以处理一个请求，并且该信息在运行时已知时，使用这种模式。

# 命令。

命令模式允许将请求封装为对象。这种转换允许您将请求作为方法参数传递，延迟或排队请求的执行，并支持可撤销的操作。

在这个例子中，我们将 on/off 的指令封装为对象，并在 Car 构造函数中将它们作为参数传递。

command

当您有一个请求队列需要处理或者您想要一个*撤销*动作时，使用这个模式。

# 翻译。

解释器模式允许在问题经常出现时为简单语言创建语法；可以考虑用简单的语言将其表示为一个句子，以便解释者可以通过解释该句子来解决问题。

在这个例子中，我们创建了一个简单的数来乘以几个数的幂。

interpreter

当您想要解释给定的语言，并且可以将语句表示为抽象语法树时，请使用这种模式。

# 迭代器。

迭代器模式允许访问集合中的元素，而不暴露其底层表示。

在这个例子中，我们创建了一个简单的迭代器，它有一个包含元素的数组，使用方法 next()和 hasNext()我们可以遍历所有的元素。

iterator

当您想要访问一个对象的内容集合而不知道它在内部是如何表示的时候，可以使用这种模式。

# 调解员。

中介模式通过定义一个封装了一组对象如何交互的对象，减少了对象之间混乱的依赖关系。

在这个例子中，我们创建了一个类 mediator TrafficTower，它现在允许我们从飞机实例中获取所有位置。

mediator

当一组对象以复杂的方式相互通信时，使用这种模式。

# 纪念品。

memento 模式允许捕获和具体化一个对象的内部状态，以便该对象可以在以后恢复到这个状态。

在本例中，我们创建了一种简单的方法来存储值并在需要时恢复快照。

memento

当您希望生成对象状态的快照以便能够还原对象以前的状态时，请使用此模式。

# 观察者。

观察者模式允许在对象之间定义一对多的依赖关系，这样当一个对象改变状态时，它的所有依赖对象都会得到通知并自动更新。

在本例中，我们创建了一个简单的类产品，其他类可以观察 register()方法中的注册更改，当有更新时，notifyAll()方法将与所有观察器就这些更改进行通信。

observer

当更改一个对象的状态可能需要更改其他对象，并且实际的对象集事先未知或动态变化时，请使用此模式。

# 状态。

状态模式允许对象在其内部状态改变时改变其行为。

在本例中，我们创建了一个简单的状态模式，其中的 Order 类将使用 next()方法更新状态。

state

当对象的行为依赖于它的状态，并且它在运行时的行为变化依赖于它的状态时，使用这种模式。

# 策略。

策略模式允许定义一系列算法，封装每一个算法，并使它们可以互换。

在这个例子中，我们有一组可以在购物车中应用的折扣。这里的技巧是，我们可以将要应用的函数传递给构造函数，并以这种方式更改折扣金额。

strategy

当你有许多相似的类，只是它们执行某些行为的方式不同时，使用这种模式。

# 模板法。

模板模式允许在超类中定义算法的框架，但允许子类在不改变其结构的情况下覆盖算法的特定步骤。

在本例中，我们将创建一个简单的模板方法来计算税款，并在增值税和商品及服务税(税种)中扩展该模板，这样我们就可以在多个税种中重用相同的结构。

template

当您希望让客户端只扩展算法的特定步骤，而不是整个算法或其结构时，请使用此模式。

# 访客。

访问者模式允许将算法与它们所操作的对象分开。

在本例中，我们创建了一个结构来计算两种类型员工的奖金，通过这种方式，我们可以将奖金方法扩展到越来越多类型的员工，如 CEO 奖金、VP 奖金等。

visitor

当一个对象结构包含许多类，并且您想要对依赖于它们的类的结构元素执行操作时，请使用这种模式。

# 在系列中。

[创作](/globant/design-patterns-in-javascript-creational-2a02726e4e71)

[结构性](https://blog.carlosrojas.dev/design-patterns-in-javascript-structural-106bc31953c9)

[行为](https://blog.carlosrojas.dev/design-patterns-in-javascript-behavioral-3c8f53aaa7c0)

# 参考文献。

*设计模式——JavaScript*。(2019).设计模式— JavaScript。https://designpatternsgame.com/patterns

Refactoring.Guru. (2014)。*设计图案*。[https://refactoring.guru/design-patterns](https://refactoring.guru/design-patterns)

蒂姆斯，S. (2016 年)。*掌握 JavaScript 设计模式—第二版*(第二修订版)。包装出版公司。

我希望这篇文章能派上用场，如果你有问题，不要忘记发表评论，或者通过 [Twitter](https://mobile.twitter.com/carlosrojas_o) 联系我。再见:)