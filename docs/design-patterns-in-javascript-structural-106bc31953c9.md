# JavaScript 中的设计模式:结构化

> 原文：<https://medium.com/globant/design-patterns-in-javascript-structural-106bc31953c9?source=collection_archive---------0----------------------->

![](img/7276e2cd67923292eb1bed17b78d7a8c.png)

Photo by [MagicPattern](https://unsplash.com/@magicpattern?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/patterns?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

设计模式是将众所周知且经过测试的解决方案应用于软件开发中常见问题的好方法。*结构*模式是一个设计模式类别，被授予与对象和类情况的*组合相关的常见问题。我写这篇文章的目的是为开发者提供一个简单易用的快速参考指南。*

# 适配器。

适配器模式允许类一起工作，创建一个类接口到另一个类接口。

在这个例子中，我们使用一个 SoldierAdapter 来在我们当前的系统中使用遗留方法 attack()，并支持新版本的士兵超级战士。

SoldierAdapter

当您需要使用现有的类，但是它们之间的接口不匹配时，请使用这种模式。

# 大桥。

桥接模式允许我们的类中的一个接口根据我们接收的实例和我们需要返回的实例来构建不同的实现。

在这个例子中，我们在士兵类型和武器类型之间建立了一个桥梁，这样我们就可以将武器实例正确地传递给士兵。

Soldiers Bridge

当您需要在运行时从抽象中使用特定的实现时，请使用这种模式。

# 复合材料。

复合模式允许创建具有原始项目或对象集合属性的对象。集合中的每一项都可以容纳其他集合本身，从而创建深度嵌套的结构。

在本例中，我们创建了一个存储在机柜中的计算设备子系统，每个元素都可以是不同的实例。

Composite

当您想要表示对象的层次结构时，请使用此模式。

# 装潢师。

装饰模式允许在运行时动态扩展对象的行为。

在这个例子中，我们使用装饰器来扩展脸书通知的行为。

Notification Decorator

当您希望在运行时向一个对象添加扩展而不影响其他对象时，请使用此模式。

# 门面。

facade 模式为子系统中的一组接口提供了简化的接口。Facade 定义了一个更高级的接口，使得子系统更容易使用

在这个例子中，我们创建了一个简单的接口 **Cart** ，它将几个子系统的所有复杂性抽象为**折扣、运输、**和**费用**。

Facade Pattern

当您希望为复杂的子系统提供简单的接口时，可以使用这种模式。

# 轻量级。

Flyweight 模式通过高效地共享大量细粒度对象来节省内存。共享的 flyweight 对象是不可变的，也就是说，它们不能被改变，因为它们代表了与其他对象共享的特征。

在这个例子中，我们是

Flyweight

当应用程序使用大量小对象并且它们的存储很昂贵或者它们的标识不重要时，使用这种模式。

# 代理。

代理模式为另一个对象提供代理或占位符对象，并控制对该另一个对象的访问。

在这个例子中，我们是

Proxy

当一个对象受到严重约束，无法履行其职责时，使用这种模式。

# 在系列中。

[创作](/globant/design-patterns-in-javascript-creational-2a02726e4e71)

[结构性](https://blog.carlosrojas.dev/design-patterns-in-javascript-structural-106bc31953c9)

[行为](https://blog.carlosrojas.dev/design-patterns-in-javascript-behavioral-3c8f53aaa7c0)

我希望这篇文章能派上用场，如果你有问题，不要忘记发表评论，或者通过 [Twitter](https://mobile.twitter.com/carlosrojas_o) 联系我。再见:)