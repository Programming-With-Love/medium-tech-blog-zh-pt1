# JavaScript 中的设计模式:创造性

> 原文：<https://medium.com/globant/design-patterns-in-javascript-creational-2a02726e4e71?source=collection_archive---------1----------------------->

![](img/c32d52e6b7e2308004ecbe6f06041049.png)

Photo by [Clark Van Der Beken](https://unsplash.com/@snapsbyclark?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/patterns?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

设计模式是将众所周知且经过测试的解决方案应用于软件开发中常见问题的好方法。*创建*模式是一个设计模式类别，授予与*创建对象*情况相关的常见问题。我写这篇文章的目的是为开发者提供一个简单易用的快速参考指南。

# 单身。

单例模式将特定对象的实例数量限制为一个。单例减少了对全局变量的需求，从而避免了名称冲突的风险。

在本例中，我们正在检查`constructor()`中是否已经存在一个`Animal` 实例，或者我们是否需要创建一个新的实例。

当您只需要一个类的实例时，请使用这种模式。

# 原型。

Prototype 模式基于现有的对象创建新的对象，并使用默认的属性值。

在这个例子中，我们可以使用`clone()`方法创建一个新的对象`Fruit`，其名称和权重与其父对象相同。

当您只能在运行时实例化类时，请使用此模式。

# 工厂。

工厂模式创建新的对象，委托在子类中实例化哪个类。

在这个例子中，`MovieFactory`决定创作什么样的电影。

当您希望子类决定创建什么对象时，请使用这种模式。

# 抽象工厂。

抽象工厂创建按主题分组的新对象，而不指定它们的具体类。

当系统应该独立于它所生产的东西是如何被构造和表示的时候，使用这种模式。

# 在系列中。

[创造性](/globant/design-patterns-in-javascript-creational-2a02726e4e71)

[结构性](https://blog.carlosrojas.dev/design-patterns-in-javascript-structural-106bc31953c9)

[行为](https://blog.carlosrojas.dev/design-patterns-in-javascript-behavioral-3c8f53aaa7c0)

我希望这篇文章能派上用场，如果你有问题，不要忘记发表评论，或者通过 [Twitter](https://mobile.twitter.com/carlosrojas_o) 联系我。再见:)