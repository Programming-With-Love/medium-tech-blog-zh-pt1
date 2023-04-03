# Object.finalize()的弃用

> 原文：<https://medium.com/oracledevs/deprecation-of-object-finalize-5e40022cb69?source=collection_archive---------0----------------------->

Java 离堆播客的第 23 集的第一部分介绍了 Java 9 中对`Object.finalize`的弃用以及弃用和终结。反对是我最关心的话题。主持人甚至提到了我的名字。谢谢你们的大声呼喊，伙计们！

![](img/5c451a1268d2722f4f92895d076b8b30.png)

我想澄清几个观点，并回答一些在节目的这一部分没有解决的问题。

# Java 终结器与 C++析构器

Java 的终结器的角色不同于 C++的析构器。在 C++中(在引入像`shared_ptr`这样的机制之前)，每当你在构造函数中用`new`创建东西时，你都需要在析构函数中对它调用`delete`。人们错误地将这种想法带到了 Java，他们认为有必要编写`finalize`方法来消除对其他对象的引用。(这从来就没有必要，幸运的是，这种做法似乎在很久以前就已经消失了。)在 Java 中，垃圾收集器会清理驻留在堆上的任何东西，因此很少需要编写终结器。

如果一个对象创建了不受垃圾收集器管理的资源，那么终结器就很有用。例如文件描述符或本机分配的(“堆外”)内存。垃圾收集器不清理这些，所以其他东西必须清理。在 Java 的早期，终结是清理非堆资源的唯一可用机制。

# 幻像引用

终结的意义在于，它允许在一个对象变得不可访问之后，但在它被实际收集之前，有最后一次清理的机会。终结的一个问题是它允许对象的“复活”。当一个对象的`finalize`方法被调用时，它有一个对`this`的引用——即将被收集的对象。它可以将`this`引用挂回到对象图中，防止对象被收集。因此，在`finalize`方法返回后，对象不能简单地被收集。相反，垃圾收集器必须再次运行*以确定该对象是否真的不可到达并因此可以被收集。*

*参考包`java.lang.ref`在 JDK 1.2 中就被引入了。这个包包括几个不同的引用类型，包括`PhantomReference`。`PhantomReference`的显著特征是它不允许对象“复活”它通过使包含的引用不可访问来做到这一点。虚引用的持有者得到通知，被引用对象变得不可达(严格地说，*虚可达*)，但是没有办法把被引用对象取出来并把它挂回到对象图中。这使得垃圾收集器的工作更容易。*

*`PhantomReference`的另一个优点是，像其他引用类型一样，它可以被显式清除。假设有一个对象保存了一些外部资源，比如文件描述符。通常，这样的对象有一个应用程序应该调用的`close`方法来释放描述符。在引入引用类型之前，这些对象还需要一个`finalize`方法，以便在应用程序调用`close`失败时进行清理。问题是，即使应用程序已经调用了`close`，收集器也需要进行终结处理，然后再次运行*，如上所述，以便收集对象。**

**`PhantomReference`和其他引用类型有一个`clear`方法，它显式地清除包含的引用。通过对`close`方法的显式调用释放其本机资源的对象将调用`PhantomReference.clear`。这避免了后续的引用处理步骤，允许在对象变得不可访问时立即收集它。**

# **为什么现在弃用 Object.finalize？**

**一些事情已经改变了。首先， [JEP 277](http://openjdk.java.net/jeps/277) 已经阐明了 Java 9 中弃用的含义，因此它并不意味着 API 将被移除，除非`forRemoval=true`被指定。对`Object.finalize`的弃用是一种“普通”的弃用，因为它不会因为移除而被弃用。(至少，现在还没有。)**

**Java 9 中的第二个变化是引入了一个类`java.lang.ref.Cleaner`。引用处理通常相当微妙，创建一个引用队列和一个线程来处理来自该队列的引用需要做大量的工作。`Cleaner`基本上是围绕`ReferenceQueue`和`PhantomReference`的包装器，使得引用处理更容易。**

**没有改变的是，多年来，不鼓励使用终结化一直是 Java 知识的一部分。是时候正式声明了，方法就是弃用。**

# **Java SE 中删除过什么吗？**

**播客中提到了卡梅隆·波弟写于 2014 年的 [Quora 回答，他说没有任何东西从 Java 中移除。他写的时候，陈述是正确的。JDK 的各种*特性*已经被移除(比如 **apt** ，注释处理工具)，但是公共 API 从未被移除。](https://www.quora.com/Has-Sun-or-Oracle-ever-removed-a-deprecated-Java-method-in-an-official-API/answer/Cameron-Purdy?srid=vDdR)**

**但是，以下六个 API 在 Java SE 8 中已被弃用，并且已从 Java SE 9 中删除:**

1.  **`java.util.jar.Pack200.Packer.addPropertyChangeListener`**
2.  **`java.util.jar.Pack200.Unpacker.addPropertyChangeListener`**
3.  **`java.util.logging.LogManager.addPropertyChangeListener`**
4.  **`java.util.jar.Pack200.Packer.removePropertyChangeListener`**
5.  **`java.util.jar.Pack200.Unpacker.removePropertyChangeListener`**
6.  **`java.util.logging.LogManager.removePropertyChangeListener`**

**此外，在 Java SE 9 中，大约 20 个方法和 6 个模块已经被弃用，这表明我们打算在下一个主要的 Java SE 版本中删除它们。要删除的一些类和方法包括:**

*   **`java.lang.Compiler`**
*   **`Thread.destroy`**
*   **`System.runFinalizersOnExit`**
*   **`Thread.stop(Throwable)`**

**不赞成删除的模块如下:**

1.  **`java.activation`**
2.  **`java.corba`**
3.  **`java.transaction`**
4.  **`java.xml.bind`**
5.  **`java.xml.ws`**
6.  **`java.xml.ws.annotation`**

**所以，是的，我们正在认真对待移除物品！**

# **定案会被移除吗？**

**如前所述，`Object.finalize`目前不建议删除。因此，对它的反对仅仅是建议开发人员考虑迁移到替代的清理机制。推荐替换的是`PhantomReference`和新的`Cleaner`级。**

**也就是说，我们最终想要摆脱终结化。它给垃圾收集器增加了额外的复杂性，并且经常会导致性能问题。**

**然而，在我们能够摆脱它之前，我们需要从 JDK 中去除它的用途。这不仅仅是移除`finalize`的覆盖并重写代码以使用`Cleaner`来代替。问题是 JDK 中有一些公共 API 类覆盖了`finalize`并指定了它的行为。反过来，*的子类*可能会覆盖`finalize`并依赖于`super.finalize()`的现有行为。移除`finalize`方法会将这些子类暴露给潜在的不兼容的行为变化。这需要仔细调查。**

**可能还会有一个过渡期，在此期间对`finalize`方法的调用由命令行选项控制。这将允许对应用程序进行测试，看它们是否能在没有终结的情况下处理。只有经过一段过渡期后，我们才会考虑完全取消敲定机制。出于二进制兼容性的目的，我们甚至可以保留`finalize`方法声明，即使调用它的机制已经被移除。**

**正如你所看到的，移除终结化需要一个跨越几个 JDK 版本的漫长的过渡期，需要几年时间。这就更有理由开始反对了。**

***原载于 2017 年 4 月 18 日*[*stuartmarks.wordpress.com*](https://stuartmarks.wordpress.com/2017/04/17/deprecation-of-object-finalize/)*。***