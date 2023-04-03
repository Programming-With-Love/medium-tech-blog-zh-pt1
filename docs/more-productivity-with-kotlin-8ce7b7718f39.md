# 使用 Kotlin 提高生产力

> 原文：<https://medium.com/androiddevelopers/more-productivity-with-kotlin-8ce7b7718f39?source=collection_archive---------0----------------------->

![](img/7a50ec3b77867a5996e2dbb5d88ea4d2.png)

Kotlin 以简洁著称，这在实践中转化为更高的生产率。更有甚者，67%使用 Kotlin 的专业 Android 开发人员表示，kot Lin 提高了他们的工作效率。在这篇博文中，我将分享 Kotlin 提高我们合作伙伴的工程师生产力的几种方法，并看看 Kotlin 在这方面的功能。

> 接受调查的使用 Kotlin 的专业 Android 开发人员中，有 67%表示 kot Lin 提高了他们的工作效率

# 简洁、简单和高效

科特林的简洁对发展的所有阶段都有影响:

*   作为作者这意味着你可以专注于你需要解决的问题，而不是语法。更少的代码意味着更少的测试和调试，以及更少的产生错误的机会。
*   **作为评审或维护人员**这意味着你需要阅读的代码更少，更容易理解代码的作用，因此评审或维护也更容易。

一个例子来自 Flipkart 的团队:

> “在一次内部调查中，50%的开发人员提到，如果模块是用 Kotlin 编写的，他们会提供更小的评估(来完成一个特性)。”(Flipkart)

# Kotlin 功能和生产力

Kotlin 的大部分特性可以提高工作效率，这是因为其简洁性和可读性更高，所以让我们来看看一些最常用的特性。

# 默认参数和生成器

在 Java 编程语言中，当构造函数的某些参数是可选的时，您通常会选择两条路中的一条:

*   添加多个构造函数
*   应用[构建器模式](https://en.wikipedia.org/wiki/Builder_pattern)

在 Kotlin 中，由于使用了默认参数，这两者都是不必要的。默认参数允许您在没有额外样板文件的情况下实现函数重载。

> 当 Cash App 团队开始使用 Kotlin 时，他们能够消除许多构建者，并减少他们需要编写的代码量。在某些情况下，他们节省了 25%的代码量。

例如，下面是一个`Task`对象的实现，其中任务名称是使用构建器或使用默认参数时唯一的强制参数:

从我们的 [Kotlin 词汇博客文章](/androiddevelopers/dont-argue-with-default-arguments-2245b2c752c)中找到更多关于默认参数的信息。

# 对象和单件

[单例模式](https://en.wikipedia.org/wiki/Singleton_pattern)可能是软件开发中最常用的模式之一——它帮助你创建一个对象的单个实例，这个实例可以被其他对象访问和共享。

要创建一个 singleton，你需要控制对象的创建方式，只允许它有一个实例，并确保代码是线程安全的。在 Kotlin 中，你只需要一个关键词:`object`。

# 运算符、字符串模板等等

Kotlin 语言的简洁性和简单性体现在运算符重载、析构或字符串模板等特性中——生成的代码非常易读。

比如说，我们有一个藏书的图书馆。要从库中删除一本书，然后只处理标题并打印它，我们编写的代码可以是这样的:

以下是使用的 Kotlin 功能:

*   使用[运算符重载](https://kotlinlang.org/docs/reference/operator-overloading.html)来实现`-=`
*   `val (title, author) = book`用[解构](https://kotlinlang.org/docs/reference/multi-declarations.html)
*   `println(“Borrowed $title”)`使用[字符串模板](https://kotlinlang.org/docs/reference/multi-declarations.html)

# 结论

Kotlin 使读写代码变得容易。像 [singleton](/androiddevelopers/the-one-and-only-object-5dfd2cf7ab9b?source=false---------0) 或 [delegation](/androiddevelopers/delegating-delegates-to-kotlin-ee0a0b21c52b) 这样的模式是语言的一部分，消除了编写大量代码的需要，这些代码会导致引入错误和更高的维护负担。类似[字符串模板](https://kotlinlang.org/docs/reference/multi-declarations.html)、 [lambda 表达式](https://kotlinlang.org/docs/reference/lambdas.html#lambda-expressions-and-anonymous-functions)、[扩展函数](https://kotlinlang.org/docs/reference/functions.html#extension-functions)、[运算符重载](/androiddevelopers/code-expressivity-with-operator-overloading-ada22a0ca633)等功能，让代码更加简洁明了。编写的代码越少，需要阅读的代码就越少，需要维护的代码就越少，错误就越少，生产率就越高。

阅读更多关于如何使用 Kotlin 构建更好的应用的信息，并通过阅读我们的[案例研究来了解开发者如何从 Kotlin 中受益。要开始学习世界上最受欢迎的语言之一](https://developer.android.com/kotlin/stories)[科特林语](http://d.android.com/kotlin)，请查看我们的[入门页面](https://developer.android.com/kotlin/first)。