# 数据类——保存数据的经典方式

> 原文：<https://medium.com/androiddevelopers/data-classes-the-classy-way-to-hold-data-ab3b11ea4939?source=collection_archive---------3----------------------->

![](img/f1f064469350b2336414178de85f7e03.png)

## 科特林词汇:数据类

一只小狗有一个名字，一个品种和一大堆可爱之处。要建模一个只保存数据的类，您应该使用一个`data class`。编译器通过自动生成`toString()`、`equals()`和`hashCode()`来简化您的工作，并提供开箱即用的[析构](/androiddevelopers/breaking-down-destructuring-declarations-e21334ac1e9)和复制功能，让您专注于需要表示的数据。请继续阅读，了解更多关于数据类的其他优点、它们的限制，并了解它们是如何实现的。

# 使用概述

若要声明数据类，请使用 data 修饰符，并将类的属性指定为构造函数中的 val 或 var 参数。与任何函数或构造函数一样，您也可以提供默认参数，您可以直接访问和修改属性，并在您的类中定义函数。

但是，与普通课程相比，你有几个优势:

*   `**toString()**` **、** `**equals()**` **和** `**hashCode()**` **由 Kotlin 编译器为您实现**，避免了一系列可能导致 bug 的人为小错误:比如每次添加或更新属性时忘记更新它们、实现`hashCode`时出现逻辑错误、在实现`equals`时忘记实现`hashCode`等等。
*   **破坏**
*   **通过调用`copy()`方法复制**的简易性:

# 限制

数据类有一系列的限制。

# 构造函数参数

数据类是作为数据容器创建的。要执行这个角色，您必须向主构造函数传递至少一个参数，这些参数需要是`**val**` **或** `**var**` [**属性**](https://kotlinlang.org/docs/reference/properties.html) 。试图在没有`val` / `var`的情况下添加参数会导致编译错误。

作为最佳实践，考虑使用`val` s 而不是`var` s，以提高不变性。否则，可能会出现微妙的问题，例如当使用数据类作为`HashMap`对象的键时，因为当`var`值改变时，容器可能会进入无效状态。

类似地，试图在主构造函数中添加一个`vararg`参数也会导致编译错误:

由于`equals()`在 JVM 上对数组和集合的工作方式，不允许使用`vararg`。安德烈·布雷斯拉夫[解释道](https://blog.jetbrains.com/kotlin/2015/09/feedback-request-limitations-on-data-classes/):

> 集合在结构上是比较的，而数组不是，对它们来说，`equals()`简单地诉诸于引用相等:this `===` other。

# 遗产

数据类可以从接口、抽象类和类继承，但不能从其他数据类继承。数据类也不能标记为`open`。比如添加`open`会导致错误:`Modifier ‘open’ is incompatible with ‘data’`。

# 在后台

让我们检查一下 Kotlin 到底生成了什么，以便能够理解这些特性是如何实现的。为此，我们将查看 Java 反编译代码:工具-> Kotlin ->显示 Kotlin 字节码，然后按下反编译按钮。

# 性能

与常规类一样，`Puppy`是一个 `public final class`，包含我们定义的属性以及它们的 getters 和 setters:

# 构造者

生成了我们定义的构造函数。因为我们在构造函数中使用了一个默认参数，所以我们也得到第二个合成构造函数。

要了解关于默认参数和生成代码的更多信息，请查看这篇博文。

# toString()，hashCode()，等于()

Kotlin 生成`toString()`、`hashCode()`和`equals()`方法。当您修改数据类、更新属性时，会自动为您生成正确的方法实现。这样你就知道`hashCode()`和`equals()`永远不会不同步。下面是他们如何寻找我们的`Puppy`类:

虽然`toString`和`hashCode`非常简单，看起来就像你实现它的方式，但是 equals 使用了执行结构等式的`[Intrinsics.areEqual](https://github.com/JetBrains/kotlin/blob/master/libraries/stdlib/jvm/runtime/kotlin/jvm/internal/Intrinsics.java#L166)`:

```
public static boolean areEqual(Object first, Object second) {
    return first == null ? second == null : first.equals(second);
}
```

通过使用方法调用而不是直接实现，Kotlin 语言开发人员获得了更多的灵活性，因为如果需要，他们可以在未来的语言版本中更改 areEqual 的实现。

# 成分

为了支持析构，数据类生成只返回一个字段的`componentN()`方法。组件号遵循构造函数参数的顺序:

从我们的[科特林词汇贴](/androiddevelopers/breaking-down-destructuring-declarations-e21334ac1e9)中找到更多关于析构的信息。

# 复制

数据类生成一个`copy()`方法，可用于创建对象的新实例，保留 0 或更多的初始值。您可以将`copy()`看作一个方法，它获取数据类的所有字段作为参数，字段的值作为默认值。了解了这些，你就不会惊讶 Kotlin 生成 2 个`copy()`方法:`copy`和`copy$default`。后者是一种合成方法，确保当没有为参数传入值时，使用基类中的正确值:

# 结论

数据类是最常用的 Kotlin 特性之一，有一个很好的理由——它们减少了您需要编写的样板代码，支持像析构和复制对象这样的特性，并让您专注于重要的东西:您的应用程序。