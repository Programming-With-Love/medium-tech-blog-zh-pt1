# 没有空虚的生活

> 原文：<https://blog.kotlin-academy.com/living-without-null-da9b695c908b?source=collection_archive---------0----------------------->

![](img/6c50ca33c99a24d70aa5d5168afde8a9.png)

Image credit [Jesus Kiteque](https://unsplash.com/@jesuskiteque?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

写代码时，我们通常可以避免使用`null`。它代表一种状态。

在 Kotlin 中，有许多方法可以避免使用 null。在本文中，我们将展示如何避免使用`null`来表示*空*状态。

例如，链表的典型 Java 定义是

Typical Java implementation of a linked list node

在这个 Java 实现中，`null`代表空列表。使用`null`有效。然而，我们招致了与使用空值相关的所有危险。空指针异常是使用`null`的一个危险。有没有更好的办法？让我们探索科特林。

Kotlin 可以模拟链表的 Java 实现:

Kotlin implementation of a Java linked list node.

像它的 Java 对应物一样，这种实现是有代价的。它使用可空的`LinkedList<T>?`。我们能进一步改进我们的代码吗？答案是肯定的。

从`data class` 中移除可空类型`LinkedList<T>?`的一个简单方法是从代码中移除问号:

Impractical Kotlin definition of a linked list

第 3 行的`data class`定义不切实际。此外，第 6 行和第 7 行不会编译。第 3 行表示任何链表都有一个有效负载`T`和对另一个非空链表的引用。换句话说，链表的长度是无限的。

上面的定义有错误。它不考虑空列表。让我们如下定义一个链表:

链表可以是:

1.  具有两个属性的节点:类型为`T`的非空负载和非空链表；或者
2.  终端对象。

我们将使用一个`sealed class`来强制一个链表要么是类型 1 要么是类型 2 *，仅此而已*。首先，让我们定义一下`Node<T>`:

sealed class definition with Node<T>

我们会用科特林的`object`来定义`Terminal`，因为所有的`Terminals`都是一样的。`Terminal`是独生子。

complete sealed class definition of a linked list

为了方便起见，`next`属性的默认值为`Terminal`。见第 4 行。我们可以在不使用`Terminal`对象的情况下编写一个`LinkedList` 。见第 9 行。

让我们探索一下链表定义的一些实际应用。例如，我们可以将`toString()`方法定义如下:

1.  如果`LinkedList`是一个`Node<T>`，那么返回`payload`和`next`的连接。
2.  如果`LinkedList`是一个`Terminal`，那么返回空字符串。

toString definition of a linked list

尺寸是另一个例子。尺寸的定义是:

1.  如果`LinkedList`是一个`Node<T>`，那么大小就是 1 加上节点的`next`属性的大小。
2.  如果`LinkedList`是一个`Terminal`，那么大小为零。

使用 Kotlin 的`when`作为表达式，我们可以递归地定义大小为

recursive implementation of size()

我们将链表定义为一个`sealed class`。所以，第 1 行中的`when`是一个表达式。如果`when`是一个表达式，那么`when`一定是穷举的:`Node<T>`和`Terminal`。

作为替代，我们可以将`sizeRecursively()`的定义放在`sealed class`中:

sizeRecursively() defined within the sealed class

功能`sizeRecursively()`可能不是很高效。它不是尾递归的。所以，`sizeIteratively()`的迭代实现可能更好:

iterative definition of size()

我们已经表明`null`代表一种状态。使用空值会带来危险，比如空指针异常。我们已经展示了我们可以通过使用一个`sealed class`来枚举一个链表的所有可能的状态。

通过使用`sealed class`作为对象所有可能状态的枚举，代码不使用`null`。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察 Twitter](https://twitter.com/ktdotacademy) 在 medium 上关注我们。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)

喜欢的话记得**拍**。请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。