# React Native vs Xamarin:跨平台开发的最佳选择

> 原文：<https://medium.com/quick-code/react-native-vs-xamarin-best-choice-for-cross-platform-development-ae8c97176fab?source=collection_archive---------5----------------------->

![](img/588fa805dc0821f54103213ffa20db14.png)

当我们谈论跨平台开发时，首先想到的相关框架是 React Native 和 Xamarin。不幸的是，没有那么多的开发人员在这两方面都足够熟练。如果您是一名面临“Xamarin vs React Native”困境的初级程序员，或者只是喜欢在项目实现中使用跨平台方法，我们随时准备帮助您选择最适合您的情况的开发环境。

# React Native:简要概述

[React Native (RN)](https://facebook.github.io/react-native/) 是一个基于 JS 的框架，由脸书团队创建，于 2015 年上半年发布。以 ReactJS 库为基础，该框架采用本地组件作为前端(借助特殊的桥而不是传统的 WebViews 与 JavaScript 代码进行交互)。从实践的角度来看，这允许为 iOS 和 Android 创建“反应式”(性能方面)的跨平台应用程序。

![](img/092b86e92d8e86516afe6665413b038b.png)

React Native 目前被数万家公司使用。它尤其受到初创公司开发者的青睐，因为它完全免费，不需要任何额外的费用。尤其值得一提的是，《卫报》的新闻网站是在 RN 以及全球使用的脸书和 Skype 应用程序的帮助下建立的。

# Xamarin:简要概述

Xamarin 成立于 2011 年，目前归微软所有。该开发环境旨在使用. NET 在 C#上创建跨平台移动软件。Xamarin 支持 iOS、Mac OS、Android 和 Windows Phone。

![](img/f4b5fcb375b239af98b5a37d62d507a4.png)

Xamarin 包括几个一致的部分:

*   xa marin iOS(c#的类库，提供对 iOS SDK 的访问)；
*   xamarin Android(c#的类库，提供对 Android SDK 的访问)；
*   iOS 和 Android 编译器；
*   Visual Studio 插件。

每天有超过 15，000 家公司在他们的开发中使用它。用 Xamarin 构建的著名应用包括 Novarum 和 Story。

# React Native 和 Xamarin 的功能和主要特性的比较

请看一下 React Native 与 Xamarin 的详细比较，以便能够合理地决定在您的项目中使用哪一个:

## App 工作原理

React Native 曾经是实现数据绑定过程的创新解决方案。特别地，该框架采用单向数据绑定，即它将模型定义与表示绑定在一起，并且不涉及单独的管理者来帮助检测用户是否在视图中定制了定义。这反过来又为应用程序提供了性能提升。尽管如此，RN 也支持双向数据绑定(手动实现),以提供代码的相互一致性并减少复杂错误的机会。

至于 Xamarin，它涉及一个更严格的 MVVM 架构，具有更多开发人员熟悉的双向数据绑定，这提供了完美的团队合作条件(多个部门可以同时参与开发过程——例如设计和编程部门)。基本上，这是使用每个平台时的主要区别。因此，如果您需要严格面向性能的功能和更灵活的方法，请选择 RN。

## 内置工具集

React Native 因其大量的内置组件而在开发人员中众所周知，所有这些组件都可以在软件包管理器 [nom](https://www.npmjs.com/package/react-native) 中获得。您可以将精力集中在构建应用程序架构和制作非常具体的功能模板上，同时还可以使用现成的工具和功能(甚至有专门的文档)。

谈到 Xamarin，除了标准的。NET 类，开发人员总是可以通过 Xamarin 访问 Android 和 iOS 平台原生的类。分别是 Android 和 Xamarin.iOS。这允许开发与本机相同的软件。

## 汇编

在 React Native 中，编译的工作方式如下:首先创建一个与 UI 兼容的原生包装器；整个 JSX/TSX/flexbox 集被解析到其中，而业务逻辑用户代码被传输到 JS 中。在这种情况下,“实时”类型的编译(JIT)对 iOS 应用程序不可用——这可能会稍微减慢软件代码执行过程。

在 Xamarin 中，编译的工作方式略有不同。JIT 编译在这里也不适用于 iOS，所以可以用“提前”(AOT)类型的编译来代替。它的执行速度甚至更快。

## 学习曲线

React Native 需要 JavaScript 知识来创建应用程序。JS 是最广泛使用的编程语言之一。另一方面，这排除了借助第三方解决方案编写你的跨平台软件的某些部分，或者引入新技术、语言或库的可能性。然而，RN 非常容易使用:开发人员倾向于强调框架陡峭的学习曲线。

为了创建一个基于 Xamarin 的应用，你不需要了解 Java/kot Lin(Android)或 Objective-C/Swift(iOS)。由于 Xamarin，您仍然可以完全访问 SDK 平台的所有功能。Android 和 Xamarin.iOS 库(也就是说，你不太可能需要任何额外的工具——你需要的一切可能已经包含在 Xamarin 包中了)。因此，使用 Xamarin 所需要的只是 C#和。NET 技术，以及在两种平台上使用本机类的基本技能。

## 测试

一个名为 [Jest](https://jestjs.io/) 的工具通常用于测试在 React Native 上编写的应用。它经受住了时间的考验，易于访问，并具有良好的文档。

至于 Xamarin， [Xamarin UITest](https://developer.xamarin.com/api/namespace/Xamarin.UITest/) 在这里用于创建自动测试。如果您需要同时为两个平台编写 UI 测试，这将非常方便。此外，它有一个收件箱 [Xamarin 测试云](https://testcloud.xamarin.com/)支持(一个非常有用，但昂贵的功能)。

## 证明文件

成千上万的开发人员已经注意到 React 原生文档是如何容易理解和连贯地编写的。你也可以看看 Github 上的[这篇文章](https://github.com/react-community/create-react-native-app)来学习如何使用它，甚至不用安装任何软件就可以尝试 rn 功能。

Xamarin 也有很好的文档——它提供了示例和分步教程。然而，如果被问及“对本机指令还是 Xamarin 指令做出反应？”时，许多开发人员仍然倾向于更欣赏 RN 文档。

## 网上群体

如果你在 React Native 中构建应用时遇到任何问题，你将很容易在网上找到答案。它相对年轻，但非常敏锐和发达。

Xamarin 在线社区也是如此。它甚至更广泛，有更多的时间成长和扩展。Xamarin 还以个人技术支持的形式在软件的商业许可中提供解决问题的帮助。

## 定价政策

React Native 是免费的，而 Xamarin 有付费和免费两个版本(付费版本是面向企业的)。

# 那么，选择什么——反应原生还是 Xamarin？

现在，对于主要问题:最终选择什么——反应原生还是 Xamarin？

我们认为选择 Xamarin 的主要原因是通用代码的数量，这些代码可以在平台之间分发。例如，用 Xamarin。形式，高达 96%的代码可用于基于两种平台的软件实现。以 React Native 为例，它占了 90%的代码。如果最初的后端是用实现的，Xamarin 还允许在移动应用程序开发中重复使用业务逻辑后端代码的片段。NET (C#)。

如果我们在谈论性能，并且你在寻找最快的渲染和高的整体性能，RN 是你在跨平台方法方面的最佳选择(此外，程序员倾向于避免使用 Xamarin 来创建交互式软件)。

最后但同样重要的是，定价政策 Xamarin 和 React Native 都是完全免费的。

除了这些建议，我们只能补充一点，您应该仔细考虑自己的偏好和开发人员的经验。如果你是 C#高手，并且只掌握 JS 的基本技能，React Native 并不适合你(除非你打算学习而不是创建一个商业项目)。

# 结论

我们希望我们比较 React Native 和 Xamarin 的尝试能够帮助您选择最合适的跨平台软件创建框架。如果你有一个商业解决方案的想法，并且你认为这个方案能够在利基市场的竞争中立于不败之地，那就让专业人士来研究你的概念。 [Applikey Solutions](https://applikeysolutions.com/) 在跨平台应用开发方面拥有丰富的经验。[联系我们](https://applikeysolutions.com/contact)，让我们一起让你的项目成功！

【applikeysolutions.com】最初发表于[](https://applikeysolutions.com/blog/cross-platform-mobile-app-development-react-native-vs-xamarin)**。**