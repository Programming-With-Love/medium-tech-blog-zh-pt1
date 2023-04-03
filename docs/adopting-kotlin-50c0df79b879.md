# 收养科特林

> 原文：<https://medium.com/androiddevelopers/adopting-kotlin-50c0df79b879?source=collection_archive---------2----------------------->

![](img/612346c26308b9e766075a74273966fb.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## 将 Kotlin 整合到您的大型应用程序中

(This article is also available in Chinese at [WeChat](https://mp.weixin.qq.com/s/UJipNKgGPzZ1iPJBAaLJXw) / 中文版请参考 [WeChat](https://mp.weixin.qq.com/s/UJipNKgGPzZ1iPJBAaLJXw))

开发人员在会议上反复问我的一个问题是“将 Kotlin 添加到我现有的 Android 应用程序的最佳方式是什么？”如果你在一个多人的团队中工作，采用一种新的语言会变得复杂。随着时间的推移，我的答案变得更长了，我根据自己的经验对它进行了微调，将 Kotlin 添加到现有项目中，并与其他人——包括谷歌和外部开发人员社区的人——谈论他们的旅程。

这里有一个指南可以帮助你成功地将 Kotlin 引入到大型团队的现有项目中。谷歌内部的许多团队，包括 Android 开发者关系团队，已经成功地使用了这些技术。两个显著的例子是完全用 Kotlin 重写的 [2018 Google I/O 应用](https://github.com/google/iosched)，以及混合了 Java 和 Kotlin 的 [Plaid](https://github.com/nickbutcher/plaid) 。

# 如何将 Kotlin 添加到应用程序

## 指定一个科特林冠军

你应该从在你的团队中指定一个人成为科特林专家和导师开始。这个人通常是显而易见的:对使用 Kotlin 最感兴趣的人。因为你正在读这篇文章，所以很有可能是你。这个人应该尽可能多地学习 Kotlin，并研究如何最好地将 Kotlin 整合到现有应用程序中。他们应该主动分享他们的 Kotlin 知识，并成为团队中任何问题的“求助者”。这个人还应该参与 Java 和 Kotlin 代码审查，以确保更改遵循 Kotlin 约定，并促进语言互操作性(例如可空性注释)。

## 学习基础知识

当 Kotlin 冠军花时间深入研究 Kotlin 时，团队中的其他人应该建立关于 Kotlin 的基本知识。如果你的团队刚刚开始使用 Kotlin，有很多资源可以用来学习这种语言以及它如何与 Android 交互。我强烈推荐从 [Kotlin Koans](https://kotlinlang.org/docs/tutorials/koans.html) 开始，这是一系列提供 Kotlin 特色之旅的小练习。对我来说，这是学习科特林语的一种有趣方式。

Kotlin 的官方网站([kotlinlang.org](https://kotlinlang.org))提供了使用 [Kotlin 标准库](http://kotlinlang.org/api/latest/jvm/stdlib/index.html)的[参考文档](http://kotlinlang.org/docs/reference/)和在 Kotlin 中完成不同任务的分步[教程](http://kotlinlang.org/docs/tutorials/)。Android 开发者网站也有一些关于在 Android 中使用 Kotlin 的资源。

## 组建一个学习小组

当团队熟练地编写了基本的 Kotlin 之后，是时候组建一个学习小组了。Kotlin 正在快速发展，许多新功能正在开发中，例如[协程](https://kotlinlang.org/docs/reference/coroutines.html)和[多平台](https://kotlinlang.org/docs/reference/multiplatform.html)。定期的小组讨论有助于您了解即将推出的语言功能，并强化您公司的 Kotlin 最佳实践。

## 用 Kotlin 编写测试

许多团队已经发现用 Kotlin 编写测试是在他们的项目中开始使用它的一个很好的方式，因为它不会影响您的生产代码，也不会与您的应用程序包捆绑在一起。

您的团队可以编写新的测试，也可以将现有的测试转换成 Kotlin。测试对于检查代码回归很有用，并且在重构代码时增加了信心。当将现有的 Java 代码转换成 Kotlin 时，这些测试尤其有用。

## 用 Kotlin 编写新代码

在将现有的 Java 代码转换为 Kotlin 之前，先将小部分 Kotlin 代码添加到应用程序的代码库中。从一个小的类或顶级 helper 函数开始，确保将相关的[注释](https://kotlinlang.org/docs/reference/java-to-kotlin-interop.html)添加到 Kotlin 代码中，以确保 Java 代码的适当互操作性。

## 分析对 APK 规模和构建性能的影响

将 Kotlin 添加到您的应用程序可能会增加 APK 的大小和构建时间。我们建议您使用[progguard](https://developer.android.com/studio/build/shrink-code)来确保您的发布 apk 尽可能小，并减少方法数量的增加。运行 Proguard 后，Kotlin 对您的 APK 尺寸的影响应该很小，尤其是当您刚刚开始时。

对于纯 Kotlin 项目和混合语言项目(用 Java 和 Kotlin 编写)，编译和构建时间会略有增加。然而，许多开发人员认为用 Kotlin 提高写作效率是值得的。Kotlin 和 Android 团队意识到构建时间较长，并不断努力改进开发过程中的这一重要部分。您应该度量和监控项目的构建性能影响。

## 将现有代码更新到 Kotlin

一旦您的团队习惯使用 Kotlin，您就可以开始将现有代码转换为 Kotlin。

一个极端的选择是从头开始，用 Kotlin 完全重写你的应用程序。我们在 2018 年谷歌 I/O Android 应用程序中采用了这种方法，但这可能不是大多数团队的选项，因为他们需要在采用新技术的同时发布软件。幸运的是，Kotlin 和 Java 编程语言是完全互操作的，所以您可以一次迁移一个类。

更现实的方法是使用 Android Studio 中的[代码转换器](https://developer.android.com/studio/projects/add-kotlin#convert-to-kotlin-code)，它将 Java 文件中的代码转换成 Kotlin。此外，IDE 还提供了一个选项，可以将从剪贴板粘贴到 Kotlin 文件中的任何 Java 代码转换为 Kotlin。转换器并不总是产生最地道的科特林。您需要查看每个文件，但这是一个节省时间的好方法，可以看看 Kotlin 在您的代码库中是什么样子。

注意，虽然 Java 和 Kotlin 是 100%可互操作的，但它们不是源代码兼容的。不可能同时用 Java 和 Kotlin 编写一个文件。参考 [Kotlin](https://kotlinlang.org/docs/reference/java-interop.html) 和 [Android](https://android.github.io/kotlin-guides/interop.html) 指南，获取更多关于编写 Java 和 Kotlin 之间可互操作代码的技巧。

# 说服管理层采用科特林

在获得一些使用 Kotlin 的经验后，你可能会在内心深处知道 Kotlin 适合你的团队。但是，当你的领导或涉众团队不像你一样热爱数据类、智能造型和扩展函数时，你如何说服他们采用 Kotlin 呢？如何最好地解决这个问题取决于您的具体情况。下面，我们建议一些潜在的观点，你可以用数据来支持你的观点。

*   有了 Kotlin，团队的工作效率更高。您可以显示 Kotlin 和 Java 之间每个文件平均代码行数的比较数据。看到代码行减少 25%或更多是很常见的。更少的代码编写也意味着更少的代码测试和维护，允许您的团队更快地开发新功能。此外，您可以跟踪您的团队在 Kotlin 中开发一个特性与在 Java 中开发一个类似的特性相比花费了多长时间。
*   **科特林提高了应用质量。** Kotlin 的空安全特性是众所周知的，但是还有许多[其他安全特性](https://proandroiddev.com/kotlin-avoids-entire-categories-of-java-defects-89f160ba4671)来帮助你避免整个类别的代码缺陷。Pinterest 的一个想法是，当你从 Java 迁移到 Kotlin 时，跟踪你的应用程序的每个模块的缺陷率。您应该会看到缺陷率在下降。你可以在这个 [Android 开发者故事视频](https://www.youtube.com/watch?v=c6mhYGCKeaI&t=1m14s)里看 Pinterest 谈论这个。还有一个[最近的学术研究](https://www.theregister.co.uk/2018/08/02/kotlin_code_quality/)来支持这个说法。
*   **科特林让你的团队更加快乐。**快乐很难量化，但你可以找到一种方式向你的管理层展示你的团队对 Kotlin 的兴奋。根据 [2018 StackOverflow 调查](https://insights.stackoverflow.com/survey/2018#most-loved-dreaded-and-wanted)，Kotlin 是第二大最受欢迎的编程语言。
*   **行业走向科特林。**在排名前 1000 的安卓应用中，有 26%已经在使用 Kotlin。这包括 Twitter、Pinterest、微信、美国运通等重量级网站。根据 Redmonk 的说法，Kotlin 也是增长最快的第二大移动编程语言。Dice 还公布了 Kotlin 的工作数量经历了急速上升。

# 超越

在你有了一些将 Kotlin 添加到你的应用程序的实践经验后，这里有一些额外的技巧来帮助你将 Kotlin 融入到日常开发中。

## 定义特定于项目的样式约定

[Kotlin](https://kotlinlang.org/docs/reference/coding-conventions.html) 和 [Android Kotlin](https://android.github.io/kotlin-guides/) 风格指南为格式化 Kotlin 代码建立了很好的基准。除此之外，建立最适合你的团队的惯例和习惯用法也是一个好主意。

实现统一 Kotlin 风格的一个策略是定制 Android Studio 的[代码风格设置](https://www.jetbrains.com/help/idea/copying-code-style-settings.html)。另一个策略是使用棉绒。在[向日葵](https://github.com/googlesamples/android-sunflower)和[格子](https://github.com/nickbutcher/plaid)项目中，我们使用 [ktlint](https://ktlint.github.io/) 来强制代码样式。Ktlint 提供了很棒的样式默认值，这些默认值遵循标准的 Kotlin 样式指南，也可以根据您团队的特定需求进行定制。

## 按需使用

人们很容易过分使用 Kotlin 的句法糖，用`apply`、`let`、`use`和其他伟大的语言特性包装语句。一般来说，比起减少代码行，可读性更好。例如，在 Plaid 中，我们确定一个`apply`块应该至少有两行。探索最适合您团队的方式，并将其添加到您的风格指南中。

## 探索示例项目和案例研究

下面的参考资料部分列出了 Googlers 迁移到 Kotlin 的示例项目，以及将 Kotlin 添加到项目中的公司的案例研究。

# 常见问题

和任何新技术一样，都存在未知因素。以下是我们从采用 Kotlin 的开发人员那里听到的一些问题，以及解决这些问题的建议。

## 我如何说服工程师同事使用一门新语言？

在 Google I/O，我与 Kotlin 的首席语言设计师 Andrey Breslav 讨论了工程师采用 Kotlin 的各种方法。他和我一致认为，最好的方法是在您的团队中尝试一些 Kotlin 实现，然后评估它是否适合您的情况。在一天结束的时候，如果缺点大于优点，你可以放弃科特林——安德烈说他没问题！

## 学科特林不难吗？

大多数开发人员很快就掌握了 Kotlin。有经验的 Java 开发人员可以使用相同的工具用 Java 和 Kotlin 进行开发。Ruby 和 Python 开发者会在 Kotlin 中找到类似的语言特性，比如方法链接。

## Google 会继续支持 Kotlin 进行 Android 开发吗？

**是的！**谷歌承诺支持科特林。

# 最后

我希望这个指南能给你和你的团队提供灵感，让他们把 Kotlin 添加到你的应用中。虽然这些步骤可能令人望而生畏，但几乎所有与我交谈过的人都在采用 Kotlin 后发现了软件开发的新乐趣

请注意，虽然本文主要是为 Android 应用程序编写的，但是它的概念适用于任何基于 Java 的项目，从 Android 应用程序到服务器端编程。

感谢您的阅读，并祝您在将 Kotlin 添加到您的应用程序的过程中好运！

# 继续探索

以下是帮助您采用 Kotlin 的资源集合:

Kotlin 的官方语言网站是 kotlinlang.org

*   按照[教程](https://kotlinlang.org/docs/tutorials/)或 [Kotlin Koans](https://kotlinlang.org/docs/tutorials/koans.html) 编写 Kotlin 代码(你甚至可以用你的浏览器[在线](https://try.kotlinlang.org/)尝试一下)
*   浏览[参考文档](https://kotlinlang.org/docs/reference/)以使用 Kotlin [标准库](https://kotlinlang.org/api/latest/jvm/stdlib/index.html)
*   仔细阅读新特性，如[协程](https://kotlinlang.org/docs/reference/coroutines.html)和[多平台](https://kotlinlang.org/docs/reference/multiplatform.html)

— [用来自](https://developer.android.com/kotlin)[developer.android.com](https://developer.android.com)的 Kotlin 开发 Android 应用

—风格和互操作性指南

*   通用科特林[编码约定](https://kotlinlang.org/docs/reference/coding-conventions.html)
*   Android Kotlin [风格指南](https://android.github.io/kotlin-guides/index.html)
*   从 Kotlin 调用 [Java 代码的互操作性指南，](https://kotlinlang.org/docs/reference/java-interop.html)[从 Java 调用 Kotlin](https://kotlinlang.org/docs/reference/java-to-kotlin-interop.html)，以及通用 [Android 互操作性指南](https://android.github.io/kotlin-guides/interop.html)
*   定义 Android Studio 的[代码风格设置](https://www.jetbrains.com/help/idea/copying-code-style-settings.html)
*   [KtLint](https://ktlint.github.io/) —一种带有内置格式化程序的 Kotlin 棉绒

—代码示例

*   [2018 谷歌 I/O 安卓应用](https://github.com/google/iosched)
*   [格子](https://github.com/nickbutcher/plaid)
*   [向日葵](https://github.com/googlesamples/android-sunflower)
*   [Tivi](https://github.com/chrisbanes/tivi)
*   [托皮卡](https://github.com/googlesamples/android-topeka)
*   在[developer.android.com](http://developer.android.com/)上添加[代码样本](https://developer.android.com/samples/index.html?language=kotlin)

案例研究

*   base camp—“[我们如何让 Basecamp 3 的 Android 应用 100% Kotlin](https://m.signalvnoise.com/how-we-made-basecamp-3s-android-app-100-kotlin-35e4e1c0ef12) ”
*   Camera360—“[Android 开发者故事:camera 360 凭借 Kotlin 和新技术获得全球成功](https://youtu.be/r6itIxyUhc8)”(中文视频，带英文字幕)
*   Hootsuite——在 Hootsuite 的科特林时代
*   keep safe—“[将应用程序转换为 100% Kotlin 的经验教训](/keepsafe-engineering/lessons-from-converting-an-app-to-100-kotlin-68984a05dcb6)”
*   Pinterest—“[为脾气暴躁的 Java 开发人员准备的 kot Lin](/@Pinterest_Engineering/kotlin-for-grumpy-java-developers-8e90875cb6ab)”

—文章

*   [推特上的 31 天科特林](https://twitter.com/i/moments/980488782406303744)——[@ Android dev](https://twitter.com/androiddev)2018 年 3 月期间的每日科特林提示。31 条推文中的每一条都探索了 Kotlin 的一个特定功能，并可以作为一个月内提高 Kotlin 使用的指导。博文摘要也是可用的:[第一周](/google-developers/31daysofkotlin-week-1-recap-fbd5a622ef86)、[第二周](/google-developers/31daysofkotlin-week-2-recap-9eedcd18ef8)、[第三周](/google-developers/31daysofkotlin-week-3-recap-20b20ca9e205)、[第四周](/google-developers/31daysofkotlin-week-4-recap-d820089f8090)
*   [使用 Android Studio 转换到 Kotlin 的经验教训](/google-developers/lessons-learned-while-converting-to-kotlin-with-android-studio-f0a3cb41669)——合作伙伴开发者倡导者本·巴克斯特的文章
*   [将 Android 项目迁移到 kot Lin](/google-developers/migrating-an-android-project-to-kotlin-f93ecaa329b7)——文章作者 [Ben Weiss](https://twitter.com/keyboardsurfer) ，Android 开发者关系@ Google

*感谢* [*刘德明*](https://twitter.com/jmslau) *和* [*肖恩·麦克奎蓝*](https://twitter.com/objcode)