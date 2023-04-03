# 用科特林语言重新编写 AOSP 桌面时钟应用程序

> 原文：<https://medium.com/androiddevelopers/re-writing-the-aosp-deskclock-app-in-kotlin-76c836370cb?source=collection_archive---------0----------------------->

![](img/af54db00abaa2bd6e09858c62ab8c8aa.png)

Illustration by Virginia Poltrack

Android 开源项目(AOSP)应用程序的主要目标之一是为开发者提供一个如何构建 Android 应用程序的范例。作为这一承诺的一部分，AOSP 应用的开发考虑到了 Android 的最佳实践，包括使用 [Kotlin](https://kotlinlang.org/) 作为他们的开发语言。最近，为了实现这个目标，我们开始将 AOSP 桌面时钟从 Java 重构为 Kotlin。通过这个过程，我们学到了很多关于开发人员在转换他们自己的应用程序时可以期待什么。本文讨论了在这个过程中遇到的一些障碍，并提供了一些技巧，可以用来减少类似转换的工作量。您还将了解从 Java 转换到 Kotlin 的好处，重点关注桌面时钟转换带来的改进。

# 为什么是科特林？

我们选择转换 AOSP 桌面时钟应用程序，部分原因是 Kotlin 相对于 Java 的诸多优势，包括:

*   内置的空安全，可以大大减少应用程序中的空指针异常。
*   简洁，或写更少的代码做更多的工作。
*   谷歌对 Android 开发的[“Kotlin-first”支持](https://developer.android.com/kotlin/first)意味着新的 Android 开发工具和内容，如 [Jetpack 库](https://developer.android.com/jetpack)和在线培训，将首先考虑 kot Lin 用户。

此外，[许多开发者调查](https://insights.stackoverflow.com/survey/2020#most-loved-dreaded-and-wanted)将 Kotlin 列为开发者满意度最高的语言之一。当用 Kotlin 而不是 Java 来维护代码库时，所有这些好处加在一起会使过程更加顺畅。

# 转换过程

[DeskClock app](https://android.googlesource.com/platform/packages/apps/DeskClock/) 相当大，在转换开始前包含 160 个 Java 文件，总共 31914 行代码。由于其规模，代码必须进行增量转换。在 AOSP，这是通过创建一个单独的 [Soong](https://source.android.com/setup/build) 构建目标(DeskClockKotlin)来完成的，不包括任何与 Kotlin 等价的 Java 文件。Kotlin 与 Java 的互操作性使得这个过程变得简单明了，因为它允许 DeskClockKotlin 目标在转换相应的 Java 文件时不断地包含更多的 Kotlin 文件。

第一个转换步骤是从 [Android Studio](https://developer.android.com/studio) 中的 Kotlin 插件运行[自动 Kotlin 转换工具](https://developer.android.com/kotlin/add-kotlin#convert)。这个插件自动将代码从 Java 转换成 Kotlin，通常运行良好，尽管您可能会看到一些常见问题，必须手动纠正，以使 Kotlin 代码更加习惯和健壮。增量转换一次只针对一小部分应用功能，有助于确保转换代码的正确性。除了手动测试，我们还为 DeskClock 应用运行了[兼容性测试套件(CTS)](https://source.android.com/compatibility/cts) ，以检查可能的功能退化。

# 要做的手工工作

在转换过程中，在运行自动转换工具后，我们遇到了一些需要更多手工操作的问题。本节深入讨论了几个更复杂的问题，然后是对其他次要问题的简短总结。

## 无法对某些文件运行自动转换

我们遇到的最耗时的问题之一是无法在某些 Java 文件上运行 Kotlin 插件的自动转换工具。这不是一个常见的问题，在转换的前 75 个文件中只出现过两次。然而，我们确实需要要么用 Kotlin 完全重写 Java 代码，要么花时间研究这个问题。

我们第一次遇到这个问题是在[ClockDatabaseHelper.java](https://cs.android.com/android/platform/superproject/+/master:packages/apps/DeskClock/src/com/android/deskclock/provider/ClockDatabaseHelper.java;drc=ff17acface9b98eba868fff0e2d70ddc85c5e4db;l=159)文件中。在该文件中，一个 [try-with-resources](/@appmattus/effective-kotlin-item-9-prefer-try-with-resources-to-try-finally-aec8c202c30a) 块导致了这个问题。

```
try (Cursor cursor = db.query(…)) {
    // some code in here
}
```

这个 try-with-resources 语句在 Kotlin 中并不存在，尽管它在`[use](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/use.html)` [函数](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.io/use.html)中有一个对等的语句。Java 中的 try-with-resources 语句处理给定资源不再使用时的释放(即在`try`块之后)。类似地，Kotlin use 函数接受一个 lambda 表达式，并在 lambda 执行后释放调用了 use 的资源。用 Kotlin 编写代码的一种方法如下:

```
val cursor = db.query(…)
cursor.use {
   // some code in here
}
```

转换器无法自动进行此更改。在这种情况下，我们手动更新 Java 代码，在`try`块之前声明`Cursor`，而不是在 try-with-resources 语句中声明，这使得转换工具能够成功运行。

第二个无法自动转换的文件是[LogEventTracker.java](https://cs.android.com/android/platform/superproject/+/master:packages/apps/DeskClock/src/com/android/deskclock/events/LogEventTracker.java;drc=7082447b8073bd4a7dc0c6be99d8461dcd1a6f58;l=46)文件。该文件包含如下定义的函数:

```
private String safeGetString([@StringRes](http://twitter.com/StringRes) int resId) {
    return resId == 0 ? null : mContext.getString(resId);
}
```

转换工具无法处理从该函数返回的三进制表达式[。这个工具似乎无法推断出相应的 Kotlin 函数的正确返回类型(本例中为`String?`)。我们手动将该函数重写如下:](https://kotlinlang.org/docs/reference/control-flow.html#if-expression)

```
private fun safeGetString([@StringRes](http://twitter.com/StringRes) resId: Int): String? {
    return if (resId == 0) null else context.getString(resId)
}
```

在这两种情况下，重写 Java 代码的人工工作是最少的。但是，当遇到这样的问题时，转换器会跳过整个文件。这意味着对于包含小问题的大文件，在事先不知道根本原因的情况下，需要手动调查问题的来源。

我们用来发现问题的一种方法是注释掉文件中的大部分代码，然后逐步取消注释代码，直到遇到问题。这可能是一种省时的方法，可以确定哪个代码片段负责。

## 静态常数继承

我们遇到的另一个复杂问题是 Java 和 Kotlin 在继承能力上的差异。这个问题发生在[ClockContract.java](https://cs.android.com/android/platform/superproject/+/master:packages/apps/DeskClock/src/com/android/deskclock/provider/ClockContract.java)文件中，其中静态常量值是在相互继承的接口中定义的。这本身不是问题。然而，代码库中的许多地方试图通过这些接口的子接口来引用常量，这在 Kotlin 中是不可能的。

在 Kotlin 中定义静态常量以便在 Java 代码中引用的一种常见方式是在伴随对象中，在这种情况下，在定义常量的接口中。由于增量转换，从 Java 代码中引用了`ClockContract`接口，所以使用了这种伴随对象方法。Java 和 Kotlin 的区别在于，伴随对象不是从接口的父对象继承的。例如，在接口 A 的伴随对象中定义的静态常量，其中接口 B 继承自 A，将不能通过调用`B.CONSTANT_NAME`来访问。这种访问方法在 Java 中可以很好地工作。

为了解决这个问题，我们选择用调用定义这些接口的接口中的常量来替换这些接口受影响的用法。继续上一段的例子，我们将改为引用`A.CONSTANT_NAME`，而不是试图引用`B.CONSTANT_NAME`，因为`CONSTANT_NAME`是在接口 a 中定义的。这个问题也许可以通过改变这些常量继承的整体结构来解决，很可能不使用 Kotlin 中的接口。然而，所采用的方法实现起来很简单。

## 手动空性修复

在转换过程中出现的另一个常见问题是，在转换后的 Kotlin 代码中，偶尔会出现可空类型的不准确性。例如，转换后的代码错误地指定函数接受`String` 而不是`String?`参数，这可能导致运行时错误。如果转换器工具能够从 Java 代码中推断出参数或返回类型是否可以为空，那么这些问题就可以避免。您可以用`@Nullable`和`@NonNull`对 Java 代码进行注释，以向 IDE 提供做出正确决策所需的信息。否则，可以通过跟踪代码并确定是否有可能接收或返回一个`null`值来手动修复生成的 Kotlin 中的这些为空性问题。

## 其他问题

除了这些更复杂的问题，我们在转换过程中还遇到了多个更小的问题。每个问题都有一个快速解决方案，不需要花太多时间。

生成的 Kotlin 代码有时包含“===”和“！== "比较两个 Int 值时。使用“==”或“！=" [足以比较 *Int* 值](https://kotlinlang.org/docs/reference/equality.html#referential-equality)。

Java 代码中出现的一些`@NonNull`注释在生成的 Kotlin 代码中没有被删除。Kotlin 不需要这些注释，因为该语言提供了可空性安全，所以它们被移除了。

有时候，Java 函数中的单行注释会出现在生成的 Kotlin 代码中的两个不同位置。这些注释既出现在它们应该出现的地方，也出现在它们应该出现的函数之外的一般类范围内。我们删除了这些第二个实例。

生成的 Kotlin 代码排除了等效 Java 代码中存在的空行。这可能会导致一些函数被分隔成不同的逻辑部分，从而在生成的 Kotlin 代码中包含所有的行。

当接受 Long 参数的函数被传递一个 Int 值时，生成的 Kotlin 代码有时会添加“`as Long`”来尝试将 Int 转换为 Long *。*然而[正确的做法](https://kotlinlang.org/docs/reference/basic-types.html#explicit-conversions)是在`Int`上调用`toLong()`。这也适用于其他号码类型(如`Float`*等)。).*

*在 Java 中，可以重新分配函数参数。然而，在 Kotlin 中，函数参数在幕后被定义为`val`，因此不能被重新分配。在为这个场景生成的 Kotlin 代码中，在与函数参数同名的函数中定义了一个局部变量。为了避免局部变量对参数名称的[遮蔽，重命名局部变量可能是有益的，以便通过不在参数和局部变量之间共享相同的名称来增加函数的可读性。](https://stackoverflow.com/questions/49680040/why-kotlin-allows-to-declare-variable-with-the-same-name-as-parameter-inside-the)*

*当带有`private` getter 方法的`private` Java 属性被转换为 Kotlin 时，`private`关键字有时会保留在属性的`get()`访问函数上。然而，这是不必要的，因为被声明的属性`private`已经足够了。*

# *快速转换技巧*

*以下列表旨在将上述问题的更详细解释快速总结为可操作的项目，以帮助加快转换过程:*

*   *确保“===”和“！== "用于检查[参考相等](https://kotlinlang.org/docs/reference/equality.html#referential-equality)*
*   *删除 Kotlin 代码中的任何`@NonNull`或`@Nullable`注释*
*   *检查单行注释是否位于所需的位置，并且没有重复*
*   *添加回函数中删除的空行，以匹配 Java 代码的风格*
*   *修复数值之间任何不正确的转换尝试*
*   *通过重命名隐藏函数参数名称的局部变量来提高代码可读性*
*   *移除私有属性的`get()`访问器上不必要的`private`关键字*

# *结果*

*现在，大约一半的桌面时钟应用程序的 Java 文件已经被转换成 Kotlin。我们用 15，240 行 Kotlin 代码替换了 15，886 行 Java 代码。这些 Java 文件的转换大约花了一个月的工程时间才完成。通过这个 Kotlin 转换过程以及本文，AOSP 桌面时钟应用程序能够更好地实现其成为 Android 开发最佳实践范例的目标。*

*在将大约一半的 DeskClock 应用程序转换为 Kotlin 后，我们已经看到了做同等工作的代码行数的好处。代码的总行数从 15，886 行减少到 15，240 行，减少了大约 5%。虽然到目前为止这并不是一个巨大的减少，但是随着越来越多的 Java 文件被转换成 Kotlin，一些额外的样板代码可以被删除，因为 Kotlin 文件与 Java 文件的交互较少。*

*我们还研究了原始 Java 应用程序和半转换应用程序在构建时间和 APK 大小上的差异。部分转换的 Kotlin 应用程序的 APK 大小为 6237217 字节，而原始 Java 应用程序的 APK 大小为 6117353 字节。这种非常轻微的大小增加，大约 2%，表明 Kotlin 在应用程序中的引入并没有显著影响 APK 的大小。当查看这两个版本的应用程序之间的构建时间变化时，Java 应用程序的平均构建时间约为 33 秒，而部分转换的 Kotlin 应用程序的平均构建时间约为 57 秒。这些构建时间都是在一台 48 核、内存为 128 GB 的机器上执行干净的构建时记录的，没有以前编译过的类文件。*

*除了桌面时钟应用程序转换的结果，其他开发人员也同样看到了转换到 Kotlin 的好处。 [Duolingo 完成了应用程序的完整 Kotlin 迁移](https://developer.android.com/stories/apps/duolingo-kotlin),减少了 30%的行数。同样，Google Home 应用程序开始将 Kotlin 整合到他们的代码库中，[减少了 33%的 NullPointerExceptions](https://android-developers.googleblog.com/2020/07/google-home-reduces-1-cause-of-crashes.html)。70%的顶级 1k 应用程序也包含 Kotlin 代码，60%的专业 Android 开发人员使用 Kotlin。*

# *后续步骤*

*要了解更多关于 Java 到 Kotlin 的 Android 转换过程，请查看 Android 上的 Kotlin 入门文档或面向 Java 开发人员的 to Kotlin 路径。如果你想看看代码库，你可以通过跟随[下载源代码](https://source.android.com/setup/build/downloading)文档来查看 AOSP 源代码。*