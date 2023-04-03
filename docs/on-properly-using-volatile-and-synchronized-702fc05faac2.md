# 正确使用易失性和同步

> 原文：<https://medium.com/google-developer-experts/on-properly-using-volatile-and-synchronized-702fc05faac2?source=collection_archive---------1----------------------->

![](img/3622908b4f0bb37a396678231f9b92df.png)

在过去的几周里，我一直在写关于[瞬态](/google-developer-experts/diving-deeper-into-the-java-transient-modifier-3b16eff68f42#.tes59mm9a)修饰语和 Java 中不同类型的[引用](/google-developer-experts/finally-understanding-how-references-work-in-android-and-java-26a0d9c92f83#.ixg2oyvhj)。我想保留 Java 中未充分使用/误用的主题，并在本周为您带来**易变性**和**同步**修饰语。

多线程是一门需要多年才能掌握和正确理解的完整学科。我们将在本文中做一个简短的介绍。

在计算中，可以从不同的线程同时访问一个资源。这可能会导致不一致和数据损坏。线程*线程*访问并修改资源。同时，线程 *ThreadB* 开始访问相同的资源。数据可能已损坏，因为它正在被同时修改。让我们分析一个没有任何保护的例子:

如果您执行这个命令，结果是折衷的和不确定的。每次您都会得到不同的随机输出。这是因为每个线程在不同的时刻执行。

```
Starting Thread 1 output:
Starting Thread 2 output:
Selected number is: 5
Selected number is: 4
Selected number is: 3
Selected number is: 2
Selected number is: 5
Selected number is: 4
Selected number is: 1
Selected number is: 3
Selected number is: 2
Selected number is: 1
Thread Thread 1 finishing.
Thread Thread 2 finishing.
```

两个修饰符都处理多线程和保护代码段免受线程访问。我的直觉是**同步**比**易变性**被更广泛地使用和理解，所以我将在我的文章开始解释它是如何工作的。我们以后也需要它来理解与**挥发性**的区别。

# 同步修改器

**同步的**修饰符可以应用于语句块或方法。 **synchronized** 通过确保代码的关键部分不会被两个不同的线程同时执行来提供保护，确保数据的一致性。让我们应用前面示例中的修饰符 **synchronized** ，看看它将如何受到保护:

请注意，在本例中，我们在运行函数 **printCount()** 的部分添加了 **synchronized** 。如果您现在执行此函数，结果将始终相同:

```
Starting Thread 1
Starting Thread 2
Selected number is: 5
Selected number is: 4
Selected number is: 3
Selected number is: 2
Selected number is: 1
Thread Thread 1 finishing.
Selected number is: 5
Selected number is: 4
Selected number is: 3
Selected number is: 2
Selected number is: 1
Thread Thread 2 finishing.
```

现在**同步**已经解释过了，让我们来看看**的易变性**。

# 挥发性改性剂

我们之前提到过**同步**修改器可以应用于块和方法。它们之间的第一个区别是**挥发性物质**是一种可以应用于野外的改性剂。

关于 Java 内存和多线程如何工作的小说明。当我们在多线程环境中工作时，每个线程都在它们正在处理的变量的本地缓存中创建自己的副本。当这个值被更新时，更新首先发生在本地缓存副本中，而不是在真实变量中。因此，其他线程不知道其他线程正在改变的值。

这里**易变**改变了范式。当一个变量被声明为 **volatile** 时，它不会被存储在线程的本地缓存中。相反，每个线程将访问主内存中的变量，其他线程将能够访问更新后的值。为了正确理解，让我们比较一下所有的方法:

```
int firstVariable;
int getFirstVariable() {return firstVariable;}

volatile int secondVariable;
int getSecondVariable() {return secondVariable;}

int thirdVariable;
synchronized int getThirdVariable() {return thirdVariable;}
```

第一种方法不受保护。线程 T1 将访问该方法，创建自己的*第一变量*的本地副本并使用它。同时，T2 和 T3 也可以访问*第一个变量*并修改其值。T1、T2 和 T3 将具有它们自己的第一变量*的值*，这些值可能不相同，并且没有被复制到 java 的主存储器中，实际结果保存在主存储器中。

*另一方面，getSecondVariable()* 访问一个已经声明为 **volatile** 的变量。这意味着，每个线程仍然能够访问该方法或块，因为它没有受到 **synchronized** 的保护，但是它们都将从主存储器访问相同的变量，并且不会创建它们自己的本地副本。每个线程将访问相同的值。

从前面的例子中我们可以想象，getThirdVariable()一次只能被一个线程访问。这确保了变量在所有线程执行过程中保持同步。

# 易失性和同步的有用性

读完这篇短文后，你可能会想到一个问题。我理解理论上的含义，但实际上是什么呢？**同步**在这一点上可能更容易理解，但是什么时候应用**易变**修饰符是有用的呢？我喜欢展示一个例子来提供更好的理解。

想到一个*日期*变量。*日期*变量总是需要相同，似乎它定期更新。每个访问被声明为 **volatile** 的 *Date* 变量的线程将总是显示相同的值。

关于线程安全有两个主要方面。一个是执行控制，一个是内存可见性。鉴于 **volatile** 提供了内存可见性(所有线程将从主内存中访问相同的值),没有执行控制的保证，这种最新的只能通过 **synchronized** 来实现。

同样，在 Java 中，对于一个**可变**变量(包括*长*和*双*变量)，所有的读写操作都是原子的。很多平台分两步执行 *long* 和 *double* 中的操作，一次写/读 32 个字节，允许两个线程看到两个不同的值。这可以通过使用 **volatile** 来避免。

感谢 [Erik Hellman](https://twitter.com/erikhellman) 对你的代码和文章的评论，你真棒。

我在我的 [Twitter 账户](https://twitter.com/eenriquelopez)中写下我对软件工程和生活的想法。如果你喜欢这篇文章或者它确实帮助了你，请随意分享和/或留下评论。这是给业余作家加油的货币。