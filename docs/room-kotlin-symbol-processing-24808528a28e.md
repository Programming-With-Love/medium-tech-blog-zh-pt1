# 房间和科特林符号处理

> 原文：<https://medium.com/androiddevelopers/room-kotlin-symbol-processing-24808528a28e?source=collection_archive---------0----------------------->

![](img/317f5bd344fb56c196f16b2b1769cbb8.png)![](img/9f6856a00fb93ce7867672ef17ae2e09.png)

Photo by [Marc Reichelt](https://unsplash.com/@mreichelt?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

[Room](https://developer.android.com/training/data-storage/room) 是 Jetpack 的数据库抽象库，它提供了在没有任何样板文件的情况下编写编译时验证的 SQL 查询的能力。它通过处理代码中的注释和生成 Java 源代码来实现这一点。

注释处理器非常强大，但是会导致构建时间的损失。对于用 Java 编写的代码来说，这通常是可以接受的，但是对于 Kotlin 来说，这种代价是相当大的，因为 Kotlin 没有内置的注释处理管道。相反，它通过从 Kotlin 代码生成存根 Java 代码来支持注释处理器，然后通过管道将其传送到 Java 编译器进行处理。

因为不是 Kotlin 源代码中的所有东西都可以用 Java 表示，所以有些信息会在翻译中丢失。类似地，Kotlin 是一种多平台语言，但是 KAPT 只在你的目标是 Java 字节码时才有效。

**满足:** [**科特林符号处理**](https://github.com/google/ksp)

随着注释处理器在 Android 上的广泛使用，KAPT 成为了构建性能的瓶颈。为了解决这个问题，Google Kotlin 编译器团队开始研究一种替代方案，为 Kotlin 提供一流的注释处理支持。当这个项目诞生时，我们为 Room 感到非常兴奋，因为它将为 Room 提供更好的支持。从 Room 2.4 开始，它对 KSP 提供了实验性支持，我们观察到编译速度提高了 2 倍，尤其是干净编译。

这篇文章不是关于教授注释处理、房间或 KSP 的。相反，它是关于我们所面临的挑战和我们在增加 KSP 支持时所做的权衡。你不需要知道房间或 KSP 来理解它，但熟悉注释处理是必要的。

> 注意，我们在 KSP 变得稳定之前很久就开始使用它了。因此，我们做出的一些决定在今天可能适用，也可能不适用。

这篇文章的目的是给注释处理器作者一个良好的开端，告诉他们在项目中添加 KSP 支持时应该注意什么。

**房间工作原理简介**

房间的标注处理涉及两个步骤。有些“处理器”类遍历用户代码，验证并提取必要的信息到“值对象”中。这些值对象被传递给“Writer”类，后者将它们转换成代码。和许多其他注释处理器一样，Room 严重依赖于[自动公共](https://github.com/google/auto/tree/master/common)和`*javax.lang.model*`包(Java 注释处理 API 包)中频繁引用的类。

为了支持 KSP，我们有三个选择:

a)为 JavaAP 和 KSP 复制每个“处理器”类，它们将具有相同的值对象作为输出，我们可以将它们输入到编写器中。

b)在 KSP / JavaAP 上创建一个抽象，这样处理器可以有一个使用该抽象的实现。

c)用 KSP 替换 JavaAP，并要求开发人员也使用 KSP 来处理 Java 代码。

选项 C 实际上并不可行，因为这会对 Java 用户造成很大的干扰。随着房间数的采用，这种破坏性的变化是不可能的。在“A”和“B”之间，我们决定选择“B ”,因为处理器具有重要的业务逻辑，要把它拆开并不容易。

**满足:**[](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-main:room/room-compiler-processing/)

**在 JavaAP 和 KSP 上创建一个通用的抽象并不容易。Kotlin 和 Java 可以互操作，但是它们有不同的模型。例如，有一些特殊的类类型，比如 Kotlin 中的值类或 Java 中的静态方法。此外，Java 在类中有字段和方法，而 Kotlin 有属性和函数。**

**我们没有试图追求完美的抽象，而是决定实现*“房间需要什么”*。这实际上意味着，找到导入了`javax.lang.model`的房间中的每一个文件，并将其移动到 X 处理中的一个抽象中。于是`TypeElement`变成了`XTypeElement`，`ExecutableElement`变成了`XExecutableElement`等等。**

**不幸的是，`javax.lang.model`房间里到处都是 API。一次性创建这些 X 类会给评审者带来不可恢复的精神负担。因此，我们需要一种迭代实现的方法。**

**另一方面，我们需要证明这是可行的。所以我们首先[原型化](https://android-review.googlesource.com/c/platform/frameworks/support/+/1362062)它，一旦我们确信这是一个合理的选择，我们就[用他们自己的测试一个接一个地重新实现所有的 X 类](https://android-review.googlesource.com/c/platform/frameworks/support/+/1362102)。**

**我所说的实现*“what Room needs”*的一个很好的例子可以在关于类中字段的[这个变化](https://android-review.googlesource.com/c/platform/frameworks/support/+/1362165/6/room/compiler-xprocessing/src/main/java/androidx/room/processing/javac/JavacTypeElement.kt)中看到。当 Room 处理一个类的字段时，它总是对它的所有字段感兴趣，包括超类中的字段。所以当我们创建相应的 X-Processing API 时，我们**只添加了**获取所有字段的能力。**

**如果我们正在设计一个通用库，这将永远不会通过 API 审查。但是因为我们的目标只是一个空间，并且它已经有了一个与`TypeElement`具有相同功能的 helper 方法，复制它会降低项目的风险。**

**一旦我们有了基本的 X 处理 API 和它们自己的测试，下一步就是移动空间来使用这个抽象。这也是“实现房间需求”获得良好回报的地方。Room 已经在通用功能的`javax.lang.model`API 上有了扩展功能/属性(例如，获取`TypeElement`的方法)。我们首先更新这些扩展，使其看起来像 X 处理 API，然后在 [1 CL](https://android-review.googlesource.com/c/platform/frameworks/support/+/1361181/21/room/compiler/src/main/kotlin/androidx/room/preconditions/Checks.kt) 中将空间迁移到 X 处理。**

****API 可用性改进****

**保持 JavaAP 像 API 一样并不意味着我们不能改进。在将 Room 迁移到 X-Processing 之后，我们跟进了一系列 API 改进。**

**例如，Room 多次调用`MoreElement/MoreTypes`在`javax.lang.model`类型之间转换(例如 [MoreElements.asType](https://github.com/google/auto/blob/master/common/src/main/java/com/google/auto/common/MoreElements.java#L131) )。对它的调用通常类似于:**

**我们将所有这些调用转移到 [Kotlin 契约](https://kotlinlang.org/docs/whatsnew13.html#contracts)中，这样就足以编写:**

**另一个很好的例子是在`TypeElement`中寻找方法。通常在 JavaAP 中，你需要调用`[ElementFilter](https://docs.oracle.com/javase/7/docs/api/javax/lang/model/util/ElementFilter.html)`类来获得`TypeElement`中的方法。相反，我们在`XTypeElement`中把它变成了一个属性。**

**最后一个例子，也许是我最喜欢的一个，是可分配性。在 JavaAP 中，如果你想检查一个给定的`TypeMirror`是否可以从另一个`TypeMirror`赋值，你需要调用`[Types.isAssignable](https://docs.oracle.com/javase/8/docs/api/javax/lang/model/util/Types.html#isAssignable-javax.lang.model.type.TypeMirror-javax.lang.model.type.TypeMirror-)`。**

**这段代码真的很难读，因为你甚至无法猜测它是否验证了`type1`可以从`type2`赋值，或者相反。我们已经有了一个类似如下的扩展函数:**

**在 X-Processing 中，我们能够将它转换成`XType`上的一个常规函数，看起来很简单:**

****为 X 处理实现 KSP 后端****

**每个 X 处理接口都有自己的测试套件。我们没有编写它们来测试 [AutoCommon](https://github.com/google/auto/tree/master/common) 或 JavaAP。相反，它们是这样编写的，一旦我们有了它们的 KSP 实现，我们就可以运行测试套件来验证它是否符合 Room 的期望。**

**由于最初的 X 处理 API 是在`javax.lang.model`之后建模的，它们并不总是适合 KSP，所以我们也改进了 API 以在需要时提供更好的 Kotlin 支持。**

**这也提出了一个新问题。现有的房间代码库编写了处理 Java 源代码。当应用程序用 Kotlin 编写时，Room 只知道 Kotlin 在 Java 存根中的样子。我们决定在 X 处理的 KSP 实现中保持它的相似性。**

**例如，Kotlin 中的`suspend`函数在编译时具有以下签名:**

**为了保持相同的行为，`XMethodElement`KSP 的实现为 suspend 方法合成了一个新的参数，以及新的返回类型。(`[KspMethodElement.kt](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-main:room/room-compiler-processing/src/main/java/androidx/room/compiler/processing/ksp/KspMethodElement.kt;l=108?q=KspSuspendMethodElement&ss=androidx)`)**

> **注意，这工作得很好，因为即使在 KSP，Room 也会生成 Java 代码。当我们添加对 Kotlin 代码生成的支持时，这种情况可能会改变。**

**另一个例子是属性。一个 Kotlin 属性也可能有一个基于其签名的合成的`getter/setter`(访问器)。`XTypeElement`实现为它们合成了方法，因为 Room 期望将它们作为方法( [KspTypeElement.kt](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-main:room/room-compiler-processing/src/main/java/androidx/room/compiler/processing/ksp/KspTypeElement.kt;l=144) )找到。**

> ****注意:**我们实际上已经计划改变`XTypeElement` API 来提供`properties`而不是`fields`，因为这是 Room 真正想知道的。您可能已经猜到了，我们决定“暂时”不这样做，以减少空间的变化。希望有一天我们会实现它，当我们实现的时候,`XTypeElement`的 JavaAP 实现将改变为捆绑方法，将字段作为属性。**

**在为 X 处理添加 KSP 实现时，最后一个有趣的问题是 API 耦合。这些处理器 API 频繁地相互访问，因此如果不实现`XField` / `XMethod`(它们本身引用`XType`等)，就无法在 KSP 实现`XTypeElement`。在添加这些 KSP 实现时，我们为它们的实现部分编写了单独的测试。当 KSP 的实现变得更加完整时，我们逐渐启用了 KSP 后端的所有 X 处理测试。**

**请注意，我们在这个阶段只在 X-Processing 项目中运行测试，所以即使我们知道我们测试的是好的，我们也不知道是否所有的 Room 测试都会通过(称之为单元与集成测试:)。我们需要一种用 KSP 后端运行所有房间测试的方法。而这就是“X 处理-测试”神器诞生的时候。**

****满足:**[**X-处理-测试**](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-main:room/room-compiler-processing-testing/)**

**编写注释处理器 20%是处理器代码，80%是测试代码。您需要考虑各种可能的开发人员错误，并确保报告适当的错误消息。为了编写这些测试，Room 已经有了一个如下所示的 helper 方法:**

**在幕后，`runTest`使用了 [Google 编译测试](https://github.com/google/compile-testing)库，并允许我们简单地进行单元测试`Processors`。它合成了一个 Java 注释处理器，并在其中调用给定的`process`方法。**

**遗憾的是，Google 编译测试只支持 Java 源代码。为了测试 Kotlin，我们需要另一个库，幸运的是有 [Kotlin 编译测试](https://github.com/tschuchortdev/kotlin-compile-testing)。它确实允许我们编写针对 Kotlin 的测试，并且我们已经向该库提供了 KSP 支持。**

> ****注意:**我们后来用一个[内部实现](https://android-review.googlesource.com/c/platform/frameworks/support/+/1779266)替换了 Kotlin 编译测试，以简化 AndroidX Repo 中的 Kotlin/KSP 更新。我们还添加了更好的断言 API，这将需要针对 KCT 进行突破性的 API 更改。**

**作为能够使用 KSP 运行所有测试的最后一步，我们创建了以下测试 API:**

**这个版本和最初版本的主要区别在于，它同时用 KSP 和 JavaAP(或者 KAPT，取决于来源)运行测试。因为它多次运行测试，所以它不能返回一个结果，因为 KSP 和 JavaAP 之间的断言可能不同。**

**相反，我们想到了:**

**每次编译后，它调用结果断言(如果没有失败断言，检查编译是否成功)。我们重构了每个房间测试，看起来像:**

**剩下的很简单。将每个房间编译测试迁移到新的 API，发现新的 KSP / X 处理错误，报告，实施解决方案；冲洗并重复。由于 KSP 处于繁重的开发中，我们确实遇到了相当多的错误。每一次，我们都报告这个错误，从 Room source 链接到它，然后继续前进(或者贡献一个补丁)。每次 KSP 发布后，我们都会在代码库中搜索已修复的问题，并移除变通方法/已启用的测试。**

**一旦我们在编译测试中获得了良好的覆盖，下一步也是运行 Room 的与 KSP 的[集成测试](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-main:room/integration-tests/)。这些是实际的 Android 测试应用程序，也测试运行时的行为。幸运的是，Android 支持 Gradle 变体，所以在 KSP 和 KAPT 运行我们的 [Kotlin 集成测试](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-main:room/integration-tests/kotlintestapp/build.gradle)相当容易。**

****接下来是什么？****

**为 Room 添加 KSP 支持只是第一步。现在，我们需要更新空间来利用它。例如，房间中的所有类型检查都忽略了`nullability`，因为`javax.lang.model`的`TypeMirror`不理解`nullability`。因此，当调用 Kotlin 代码时，Room 有时会在运行时触发`NullPointerException`。有了 KSP，这些检查现在可以在房间里产生新的 KSP 错误(例如 [b/193437407](https://issuetracker.google.com/issues/193437407) )。我们添加了一些变通办法，但理想情况下，我们希望[改善](https://android-review.googlesource.com/c/platform/frameworks/support/+/1844471)空间，以正确处理这些情况。**

**类似地，即使我们支持 KSP，Room 仍然只生成 Java 代码。这个限制阻止我们添加对某些 Kotlin 特性的支持，比如[值类](https://kotlinlang.org/docs/inline-classes.html)。希望在未来，我们将增加对生成 Kotlin 代码的支持，并在 Room 中为 Kotlin 提供一流的支持。然后，可能更多:)。**

****我可以在我的项目中使用 X 处理吗？****

**嗯，不尽然；至少不像使用其他任何 Jetpack 库那样。如前所述，我们只实现了房间需要的东西。编写一个真正的 Jetpack 库有很大的开销，比如文档、API 稳定性、Codelabs 等等，而我们并没有做这些工作。话虽如此，Dagger 和 Airbnb ( [Paris](https://github.com/airbnb/paris) ， [DeeplinkDispatch](https://github.com/airbnb/DeepLinkDispatch) )都开始使用 X-Processing 来支持 KSP(并贡献他们需要的东西🙏).也许有一天我们会把它从房间里分离出来。从技术上来说，你仍然可以像在[谷歌的 Maven 仓库](https://maven.google.com/web/index.html#androidx.room)中的工件一样使用它，但是没有 API 保证你一定要[遮蔽](https://github.com/johnrengelman/shadow)它。**

****TL；dr；****

**我们为 Room 添加了 KSP 支持，尽管这并不容易，但绝对值得。如果您维护一个注释处理器，请添加对 KSP 的支持，以提供更好的 Kotlin 开发人员体验。**

**特别感谢 [Zac Sweers](https://medium.com/u/2de084182d58?source=post_page-----24808528a28e--------------------------------) 和 [Eli Hart](https://medium.com/u/9f3427a69792?source=post_page-----24808528a28e--------------------------------) 审阅了这篇文章的早期版本，他们也是了不起的 KSP 贡献者。**

****资源:****

*   **[为会议室的 KSP 支持发布跟踪器](https://issuetracker.google.com/issues/160322705)(您可以在这里找到提交的链接)**
*   **[X-加工](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-main:room/room-compiler-processing/)和[X-加工-测试](https://cs.android.com/androidx/platform/frameworks/support/+/androidx-main:room/room-compiler-processing-testing/)源代码**
*   **[KSP 源代码](https://github.com/google/ksp)**