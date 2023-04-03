# 像专业人士一样编写移动 SDK

> 原文：<https://blog.kotlin-academy.com/write-mobile-sdk-like-a-pro-43b14989508c?source=collection_archive---------1----------------------->

开发移动 SDK 的提示和指南

![](img/107dd89c1c82e01f8d4acdbe76a3848c.png)

假设你有一个为客户编写移动 SDK 的项目，它可以服务数百万用户。听起来很棒，对吗？但是…你从哪里开始呢？你是怎么设计的？编写 SDK 的正确方法是什么？这和写手机 app 一样吗？因此，在本文中，我将向您展示一些来自我个人经验的技巧和指导方针，它们可以帮助您以更好的方式编写 SDK！

首先，我们来谈谈 SDK 和 App 的一些主要区别:

*   **受众:**移动应用的主要用户是终端用户或公司，而 SDK 的主要用户是开发者。
*   **可扩展性:**由于 SDK 可以被许多不同的应用和公司使用，它可以一次安装在数百万台设备上。因此，它必须处理多个操作系统、多个设备和大量用户。
*   **集成:** SDK 需要与使用它的应用程序集成

在回顾了一些主要差异之后，让我们来看看一些提示和指导原则:

# **整合**

编写 SDK 时，集成是最重要的。
集成包括通过从 Maven 获取、初始化和在应用程序代码中使用 SDK，将 SDK 插入应用程序。

你可以做几件事让它变得尽可能简单:

*   支持最新的操作系统并使用最新的库，以避免与应用程序库冲突
*   如果你想改变你的 API，为了向后兼容，你可以放弃旧的方法并编写新的方法
*   理想情况下，整个集成过程应该只需要几分钟，所以让您团队或组织中的其他开发人员与您的 SDK 集成，并检查需要多长时间。

# 可量测性

为了确保您的 SDK 支持大量用户和设备，请采取以下步骤:

*   尝试在后台线程上完成所有繁重的提升和计算，因为它可以在低端设备上运行，因此它需要尽可能快，并在所有设备上正常工作。
*   监控内存和电池使用情况，因为它们会影响运行 SDK 的应用程序。

我知道我可能没有说什么新东西，但我的主要观点是，当你编写 SDK 时，你对运行它的设备的控制要少得多，你不知道它将用于什么目的，所以它也需要准备好并针对性能关键型应用进行优化。

# 安全性

编写 SDK 时，安全性是最重要的事情之一:

*   不要将敏感数据保存在设备上，如果必须加密的话
*   确保与服务器的所有通信都是 SSL 加密的，并使用 SSL 固定
*   删除生产中的所有日志，以避免意外暴露敏感数据

例如，这里有一篇非常深入的文章，讲述了如何让您的应用/SDK 更加安全，并为您提供了许多关于安全问题以及如何避免这些问题的见解:

[](/how-to-secure-secrets-in-android-android-security-01-a345e97c82be) [## 如何保护机密🔑在 Android 中— Android Security-01

### 在构建处理 API、从服务器接收的令牌的应用程序时，安全性是一个关键要求…

blog.kotlin-academy.com](/how-to-secure-secrets-in-android-android-security-01-a345e97c82be) [](/secure-secrets-in-android-using-jetpack-security-in-depth-android-security-02-4026b8e012f4) [## 如何保护机密🔑在 Android 中(深入)— Android 安全-02

### 在 google I/O 2019 中，Android 团队发布了名为 Jetpack Security 的安全加密库，以方便开发人员…

blog.kotlin-academy.com](/secure-secrets-in-android-using-jetpack-security-in-depth-android-security-02-4026b8e012f4) 

# **灵活性**

不同的开发人员有不同的技术偏好，因此 SDK 应该非常通用和灵活。
例如，使用 JSON 或 Google Protocol Buffers 这样的数据类型作为 API 的输入和输出，因为这给开发者带来了更多的灵活性，也更容易使用。

# 测试

开发 SDK 时，测试是必不可少的一部分，因为它增加了另一层可靠性，使代码不容易出现错误和崩溃。有许多很棒的框架可以帮助你进行测试，比如 Mockito。
Mockito 是一个非常流行的框架，用于在测试过程中模仿依赖关系，它适用于 Java 和 Kotlin:

[](https://site.mockito.org/) [## Mockito 框架站点

### Szczepan Faber and friends 为您提供摩奇托。第一批在生产中使用 Mockito 的工程师是…

site.mockito.org](https://site.mockito.org/) 

# 证明文件

作为开发人员，我们经常倾向于忽略文档部分，但是在 SDK 中，这是至关重要的。由于不同公司的其他人会阅读你的文档，你需要确保文档清晰易懂。
良好的文档意味着:

*   确切地写 API 方法做什么。
*   编写方法期望的参数和返回的内容。
*   万一方法可能抛出异常，写下所有的异常以及触发异常的原因。
*   如果方法是同步/异步，则写入。
*   写下方法是否有开发人员应该知道的边缘情况。

例如，Dokka 是一个很好的工具，可以帮助您处理文档，它可以根据文档自动生成网页:

[](https://github.com/Kotlin/dokka) [## GitHub—kot Lin/do kka:kot Lin 的文档引擎

### Dokka 是 Kotlin 的文档引擎，执行与 javadoc for Java 相同的功能。就像科特林本身一样…

github.com](https://github.com/Kotlin/dokka) 

它适用于 Java 和 Kotlin 文件。

你可以做的第二件事是将你的文档发送给你公司内部其他团队的开发人员，以确保他们也能清楚地理解。

# 摘要

第一次开发 SDK 可能是一项非常具有挑战性的任务，所以我希望这些技巧和指南能够帮助您理解从哪里开始，如何设计它，以及如何以更好的方式开发它。感谢你阅读我的文章，希望你喜欢🙂

[![](img/8769f75c3f2097afdaf2f4c1917f0a47.png)](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)