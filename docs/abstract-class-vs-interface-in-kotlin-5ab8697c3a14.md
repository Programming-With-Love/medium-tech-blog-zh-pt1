# Kotlin 中的抽象类 vs 接口

> 原文：<https://blog.kotlin-academy.com/abstract-class-vs-interface-in-kotlin-5ab8697c3a14?source=collection_archive---------0----------------------->

![](img/ef30b74d484764c819a51337094961a7.png)

Designed by katemangostar / Freepik

"抽象类和接口的区别是什么？"—这是程序员招聘过程中最常见的问题之一。流行的答案是:

*   接口不能保持状态
*   类可以有实际的功能
*   我们可以实现多个接口和一个类

我会证明那些答案不是真的。接口可以有属性，可以保存状态，但不能使用字段。只要它们不是最终的，它们就可以与实际的实体有函数关系。真正的抽象类和接口之间的显著区别是:

*   接口不能有字段
*   我们只能扩展一个类，并实现多个接口
*   类有构造函数

所以，让我们来谈谈神话:

# 接口可以有功能

在 Kotlin 中，接口可以包含带有默认主体的函数:

在这些主体中，我们可以使用`this`来引用类:

这些函数不能是最终的，它们总是可以被覆盖。这是接口和抽象类之间的一个区别，在抽象类中我们可以使函数成为 final。当我们覆盖这些函数时，我们仍然可以使用默认的 body 使用`super`:

[![](img/3abcc7bf2c50c85e442eda42ef18d587.png)](https://kt.academy/workshop/automaticTestingForBeginners)

# 接口可以有属性

Kotlin 中的 Property 表示 getter ( `val`)或 getter 和 setter ( `var`)的抽象。默认情况下，它们有在幕后使用的字段。这就是为什么这门课:

编译成类似如下的代码:

虽然属性不需要在引擎盖下有字段。例如，在下面的代码中`fullName`只是一个每次按需计算的 getter:

属性只是访问器(getters 和 setters)，所以只要它们没有任何实际值，就可以出现在接口上:

这些属性也可以有默认几何体:

![](img/b5e34cd436cce05da305b2e798ccdac6.png)

# 接口形式上可以有状态

这有点像黑客，通常也是一种不好的做法，但我只是想说明在一个接口中保存状态是可能的。我们只需要使用默认体来存储它。我们能把它存放在哪里？有一些选择，没有一个是好的。垃圾收集者会对它们进行糟糕的管理和不恰当的清理。选择较小的邪恶，我决定将它存储在一个伴随对象属性中:

请注意，这种在内存中存储的方式与我们使用字段时不同:

*   垃圾收集器没有正确管理它
*   属性与相等的类相关联，而不是与相同的类相关联(如果我们使用 reference，这可以得到改进)

**这种解决方案是违反直觉的，除非您非常清楚自己在做什么以及会有什么后果，否则不应该在实际项目中使用！**虽然知道这是可能的很好，而且它向我们展示了接口可以保存状态。这并不意味着当有人说他们不能纠正时，你应该纠正。要让他们正确地保持状态是困难和不直观的，所以我们通俗地说他们不能。让我们保持这种方式；)

[![](img/0742a8ad0cfd3851db2d28061bf6f214.png)](https://leanpub.com/effectivekotlin/c/3YYtCtqCC6a4)

# 差异

抽象类可以拥有接口所拥有的一切，此外，它们还可以拥有字段和构造函数。因此，我们可以在抽象类中正确地保存状态:

带有默认主体的函数和属性不仅可以是最终的，而且在默认情况下也是最终的。我们也可以有一个构造函数，因此将值传递给这个抽象类:

当我们有一个主构造函数时，我们可以有`init`个块:

另一方面，当我们使用它们时，我们可以实现多个接口，只扩展一个类。我们总是需要调用超类的构造函数。

这是接口相对于类的一个重要优势。在本文中，我展示了如何在称为特征或混合的模式中使用它:

[](/inheritance-composition-delegation-and-traits-b11c64f11b27) [## 继承、组成、授权和特征

### 共享公共行为是编程中最重要的事情之一。通过编程，我们代表知识…

blog.kotlin-academy.com](/inheritance-composition-delegation-and-traits-b11c64f11b27) 

## 单击👏说“谢谢！”并帮助他人找到这篇文章。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察 Twitter](https://twitter.com/ktdotacademy) 并在 medium 上关注我们。

如果您需要 Kotlin 工作室，请查看我们如何帮助您: [kt.academy](https://kt.academy/) 。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)