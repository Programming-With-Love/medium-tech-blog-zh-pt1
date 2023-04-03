# 将代表委派给 Kotlin

> 原文：<https://medium.com/androiddevelopers/delegating-delegates-to-kotlin-ee0a0b21c52b?source=collection_archive---------0----------------------->

![](img/fc82c9fa6e27477c74910d831194b1ca.png)

代表们

完成某些工作的一个方法是将工作委托给另一方。不，我不是说把你的工作委托给你的朋友，而是把工作从一个对象委托给另一个对象。

你猜怎么着，委托对软件来说并不陌生。[委托](https://en.wikipedia.org/wiki/Delegation_pattern)是一种设计模式，其中一个对象通过委托给一个助手对象来处理请求，称为*委托*。委托负责代表原始对象处理请求，并将结果提供给原始对象。

Kotlin 通过提供对类和属性委托的支持，甚至包含一些自己的内置委托，使委托变得更加容易。

# 班级代表

假设您有一个`ArrayList`用例，它可以恢复最后一个被移除的项目。基本上，您所需要的就是相同的`ArrayList`功能，并引用最后一个删除的项目。

一种方法是扩展`ArrayList`类。因为这个新类是扩展具体的`ArrayList`而不是实现`MutableList`接口，所以它与具体的`ArrayList`实现高度耦合。

如果您可以覆盖`remove()`函数来保留被删除项的引用，并将`MutableList`的其余空实现委托给其他对象，那就不好了。Kotlin 通过将大部分工作委托给一个内部`ArrayList`实例并定制其行为，提供了一种实现这一点的方法。为此，Kotlin 引入了一个新的关键词:`by`。

让我们看看班级委托是如何工作的。当您使用`by`关键字时，Kotlin 会自动生成代码，将`innerList`实例用作委托。

关键字`by`告诉 Kotlin 将功能从`MutableList`接口委托给名为 innerList 的内部`ArrayList`实例。`ListWithTrash`通过向内部`ArrayList`对象提供直接的桥接方法，仍然支持`MutableList`接口中的所有功能。另外，现在您有能力添加自己的行为。

# 在后台

让我们看看这是如何工作的。如果您看一看从`ListWithTrash`字节代码反编译的 Java 代码，您会看到 Kotlin 编译器实际上创建了包装器函数，这些函数调用内部`ArrayList`对象上的相应函数。

> 注意:Kotlin 编译器使用另一种称为[装饰模式](https://en.wikipedia.org/wiki/Decorator_pattern)的设计模式来支持生成代码中的类委托。在装饰器模式中，装饰器类与要装饰的类共享同一个接口。decorator 类保留目标类的内部引用，并包装或修饰接口提供的所有公共方法。

当您不能从特定的类继承时，委托尤其有用。使用类委托，您的类不属于任何类层次结构。相反，它共享相同的接口并修饰原始类型的内部对象。这意味着您可以在不破坏公共 API 的情况下轻松切换实现。

# 委托属性

除了类委托，你还可以使用`by`关键字来委托属性。通过属性委托，委托人负责处理对属性的`get`和`set`函数的调用。如果您需要跨其他对象重用 getter/setter 逻辑，这将非常有用，并且可以让您轻松地将功能扩展到简单的支持字段之外。

假设您有一个这样定义的`Person`类:

`**class** Person(**var** name: String, **var lastname**: String)`

这个类的`name`属性有一些格式要求。当设置了`name`时，您希望确保第一个字母是大写的，而其余的是小写的。此外，在更新名称时，您希望自动增加更新计数属性。

您*可以*实现此功能，如下所示:

虽然这是可行的，但是如果需求发生了变化，并且每次`lastname`发生变化时，您都想增加`updateCount`的值，那该怎么办呢？您可以复制/粘贴逻辑来编写自定义的 setter，但是突然您会发现自己为每个属性编写了完全相同的 setter:

两个 setter 方法几乎相同，告诉你其中一个不应该存在。使用属性委托，我们可以通过将 getters 和 setters 委托给属性来重用代码。

就像类委托一样，您可以使用`by`来委托属性，当您使用属性语法时，Kotlin 将生成代码来使用委托。

通过这一更改，您已经将`name`和`lastname`属性委托给了`FormatDelegate`类。现在让我们看看`FormatDelegate`的代码。如果您只需要委托 getter，delegate 类需要实现`ReadProperty<Any?, String>`，或者如果您需要委托 getter 和 setter，则需要实现`ReadWriteProperty<Any?, String>`。在我们的例子中，`FormatDelegate`需要实现`ReadWriteProperty<Any?, String>`，因为您希望在调用 setter 时执行格式化。

您可能已经注意到 getter 和 setter 函数中有两个额外的参数。第一个参数是`thisRef`，表示包含属性的对象。`thisRef`可用于访问对象本身，如检查其他属性或调用其他类函数。第二个参数是`KProperty<*>`，可以用来访问委托属性的元数据。

回头看看需求，让我们使用`thisRef`来访问并递增`updateCount`属性。

# 在后台

为了理解这是如何工作的，让我们看一下反编译的 Java 代码。Kotlin 编译器生成代码来保存对`name`和`lastname`属性的`FormatDelegate`对象的私有引用，以及包含您添加的逻辑的 getter/setter。

编译器还创建了一个`KProperty[]`来存储委托的属性。如果您看一下为`name`属性生成的 getters 和 setters，实例存储在索引 0 处，而`lastname`属性存储在索引 1 处。

有了这个技巧，任何调用者都可以用常规的属性语法访问委托属性。

`person.lastname = “Smith” // calls generated setter, increments count`

`*println*(**“Update count is $**person.count**”**)`

Kotlin 不仅支持委托，还在 Kotlin 标准库中提供了内置委托，我们将在另一篇文章中看到更详细的内容。

委托可以帮助您将任务委托给其他对象，并提供更好的代码重用。Kotlin 编译器创建代码让您无缝地使用委托。Kotlin 使用带有关键字`by`的简单语法来委托属性或类。在幕后，Kotlin 编译器生成所有必要的代码来支持委托，而不暴露任何对公共 API 的更改。简单地说，Kotlin 为委托生成并维护所有需要的样板代码，或者换句话说，您可以将您的委托委托给 Kotlin。