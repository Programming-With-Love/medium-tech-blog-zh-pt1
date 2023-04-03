# Kotlin 如何让编辑你的 Gradle build 不那么令人沮丧

> 原文：<https://blog.kotlin-academy.com/gradle-kotlin-the-missing-piece-of-the-puzzle-7528a85f0d2c?source=collection_archive---------0----------------------->

## 通过[Gradle builds 版本](https://github.com/jmfayard/buildSrcVersions)更好的依赖管理

![](img/8c7b0e5074ab821a52b08757447a29af.png)

朋友们好，我现在正从阿姆斯特丹和一个[科特林康夫 2018](https://kotlinconf.com/) 飞回来，我真的很喜欢。

特别是与 Gradle Kotlin-DSL 的对话[**类型安全构建逻辑给了我灵感，让我写了一个我最近一直非常喜欢的主题，甚至启动了一个在我脑海深处的 IMHO 整洁的小项目**](https://eskatos.github.io/kotlinconf2018-type-safe-build-logic/#/)

[](https://github.com/jmfayard/buildSrcVersions) [## jmfayard/buildSrcVersions

### Gradle buildSrcVersions -无痛依赖管理- jmfayard/buildSrcVersions

github.com](https://github.com/jmfayard/buildSrcVersions) 

[https://www.useloom.com/share/7edceb83fd594f319356240fcce304d5](https://www.useloom.com/share/7edceb83fd594f319356240fcce304d5)

# 一份供词

了解我的人可能会感到惊讶，我即将写关于和赞扬格雷德尔。我经常表达我和格拉德的爱恨情仇。

很多人都在关注 Gradle 性能的话题(实际上是 Android 构建工具的
)。在这方面也做了大量的工作(据我所知，很快还会有更多的工作)，但是我想在这里把重点放在问题的后半部分。

> **维护这些 build.gradle 文件可能会变得非常繁琐…**

Gradle 的优点是它为表带来了强大的功能和灵活性，构建文件具有很强的声明性，易于阅读。

但是随着强大的能力而来的是对强大工具的需求……或者是令人沮丧的用户体验。

这就是格雷尔的阴暗面。虽然`build.gradle`文件很容易被**读取**，但是它们很难被**写入**。当你需要定制它们的时候，大部分都是靠自己。通常一段时间都不行，你也不知道为什么。然后在某个时候，你复制粘贴了正确的解决方案，它起作用了，但是你也不知道为什么。

实际情况是，Groovy 的动态特性与 Gradle 中完成的所有元编程结合得很差，给了我们很差的工具支持。

好了，这就是 Gradle 和 JetBrains 一直在默默研究的 [**kotlin-dsl**](https://github.com/gradle/kotlin-dsl) 项目的背景。[快要到成熟期了](https://blog.gradle.org/gradle-kotlin-dsl-release-candidate)，现在是关注话题的好时机。

# 如何迁移

[将构建逻辑从 Groovy 迁移到 Kotlin](https://guides.gradle.org/migrating-build-logic-from-groovy-to-kotlin/) 提到了两种可能的策略

1.  *将现有的语法一点一点地移植到 Kotlin，同时保留结构——我们称之为机械移植*
2.  *朝着 Gradle 最佳实践重组您的构建逻辑，并切换到 Kotlin DSL 作为这项工作的一部分*

对于我们现有的项目，后者通常更有意义。
有一个故事说，很多 C 程序员就是从这一小步开始做 C++的。

本着这种精神，我将把重点放在你现在可以与 Gradle & Kotlin 一起迈出的实际的第一步。

# 依赖性管理

Gradle 允许我们以非常简洁的方式声明依赖关系

[https://gist.github.com/jmfayard/41fcdf1efbaa3ffbea280b4cf9c84f15](https://gist.github.com/jmfayard/41fcdf1efbaa3ffbea280b4cf9c84f15)

我对这段代码有一些问题:

1.  编辑魔术字符串是容易出错的。
2.  如何集中多模块项目中的依赖关系？
3.  什么是克朗格？
4.  有新版本的 Okio 和 RxJava 吗？

使用 Groovy，我们可以用 [dependencies.gradle 模式](https://gist.github.com/gabrielemariotti/ad6672902464ee2392d0)解决集中化问题。

对科特林做同样的事情很容易:

[https://gist.github.com/jmfayard/71735e8528d7b7883f0c1bc0ed566205](https://gist.github.com/jmfayard/71735e8528d7b7883f0c1bc0ed566205)

来自 [**buildSrc**](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:build_sources) 文件夹的代码自动可用于您所有的`build.gradle`或`build.gradle.kts`文件。

# 懒惰是程序员的一大优点

我已经为几个项目这样做了，一开始我很高兴。在我的第三个项目中，提取所有这些字符串变得很乏味。

此外，我们还没有解决克兰格尔是什么的问题。有哪些新版本？

有一个来自 Ben Manes 的非常好的插件，`[gradle-versions-plugin](https://github.com/ben-manes/gradle-versions-plugin),`曾经应用于你的 Gradle 项目回答了这个问题。所以作为一个懒惰的程序员，我想知道:我能扩展它来精确地生成我需要的 kotlin 代码吗？

事实证明这是可能的，所以让我介绍我的新 Gradle 插件！

# 包括 Gradle 版本

在您的 top `build.gradle`文件中，添加:

这个新的声明插件块是声明插件的首选方式，而不是提供更差的 kotlin DSL 用户体验的`buildscript {...}`语法。

# $ ./gradlew buildSrcVersions

这是您第一次运行的命令，每次您添加一个新的依赖项或者只是想检查有哪些可用的版本。

它首先执行连接到互联网的任务 **:dependencyUpdates** ，可能需要一段时间。这个任务生成一个 [JSON 报告](https://github.com/jmfayard/sample-synclibs/blob/syncLibs/report.json)，其中包含关于所有依赖项的元数据，Kotlinpoet 帮助将这些元数据转换成下面的代码。

[https://gist.github.com/jmfayard/5c1abb7423333c9bdf335a9c04139969](https://gist.github.com/jmfayard/5c1abb7423333c9bdf335a9c04139969)

# IDE 集成

这些生成的代码是我们体验 Gradle 的 **kotlin-dsl** 在***IntelliJ IDEA****或****Android Studio≥3.2***中提供的更好的 IDE 集成所需要的全部

[https://www.useloom.com/share/7edceb83fd594f319356240fcce304d5](https://www.useloom.com/share/7edceb83fd594f319356240fcce304d5)

我们现在已经解决了我们想要解决的问题

1.  不再有神奇的字符串，我们键入`Libs.<TAB>`，自动完成剩下的工作。
2.  我们可以在所有的`build.gradle(.kts)`文件中使用它们。
3.  要发现什么是 [*克朗格*](https://github.com/holgerbrandl/krangl) ，我们按下`<F1> Quick documentation`并点击网站链接。
4.  要知道哪些新版本可用，我们只需从 IDE 跳转到`Libs.okio`，然后从那里跳转到当前使用的`Libs.Versions.okio.` 2.0.0，一条有用的注释告诉我们 2.1.0 可用。(插件从不自动应用依赖更新，那应该是开发者的工作！！).

# 试一试

Groovy `build.gradle`和 Kotlin `build.gradle.kts`文件一经编写看起来几乎相似。完全不同的是它的编写体验，所以我会鼓励你火起来 *IntelliJ IDEA* 或者 *Android Studio≥3.2。*

我开始坦白我讨厌格雷尔的黑魔法。用科特林打开黑盒非常有用。我意识到 Gradle 只是一个很好的、有用的、有良好文档记录的 API。一个 API，您可以像使用任何其他 Java API 一样使用您的开发人员工具和技能。一个其优点对我们隐藏的 API。如果有足够的兴趣，我计划在下面的文章中更深入。

# 链接

格雷尔的迁移指南

[](https://guides.gradle.org/migrating-build-logic-from-groovy-to-kotlin/) [## 将构建逻辑从 Groovy 迁移到 Kotlin

### 如果您想要配置的插件依赖于 groovy.lang.Closure 的方法签名或者使用其他动态 Groovy…

guides.gradle.org](https://guides.gradle.org/migrating-build-logic-from-groovy-to-kotlin/) 

`gradle/kotlin-dsl`中的示例是了解事物如何工作的最佳方式之一。

[](https://github.com/gradle/kotlin-dsl) [## 格雷尔/科特林数字用户线

### 对 Gradle 构建脚本的 Kotlin 语言支持。通过在…上创建帐户，为 gradle/kotlin-dsl 的发展做出贡献

github.com](https://github.com/gradle/kotlin-dsl) 

我的插件

[](https://github.com/jmfayard/buildSrcVersions) [## jmfayard/buildSrcVersions

### Gradle buildSrcVersions -无痛依赖管理- jmfayard/buildSrcVersions

github.com](https://github.com/jmfayard/buildSrcVersions) 

你需要 Kotlin 工作室吗？请访问我们的网站,看看我们能为您做些什么。

了解卡帕头最新的重大新闻。学院、[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)、[观察推特](https://twitter.com/ktdotacademy)并在媒体上关注。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)