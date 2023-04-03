# 当使用枚举和 R8 时…

> 原文：<https://medium.com/androiddevelopers/when-using-enums-and-r8-3f8f314c0a13?source=collection_archive---------0----------------------->

![](img/7dbdff2f6243de6e06b0425e311eb83b.png)

## 科特林词汇——开启枚举和 R8 优化

无论何时使用或学习一门新语言，了解该语言中的可用特性以及这些特性是否有相关的开销是很重要的。

这在 Kotlin 中变得特别有趣，因为它提供了 Java 编程语言中没有的特性，尽管它可以编译成 Java 字节码。这是怎么回事？这些特性有相关的开销吗？如果有开销，我们能做些什么吗？

这篇文章是关于枚举和 when 语句(Java 中的 switch 语句)。我将讨论一些与何时以及如何使用 Android R8 编译器来优化您的应用程序并降低开销相关的非显而易见的开销。

# 编译程序

首先，让我们谈谈 D8 和 R8。

实际上，有三个*T2 编译器步骤来处理你为 Android 应用程序编写的 Kotlin 代码。*

## # 1 kot Lin 编译器

首先，Kotlin 编译器运行并将您的代码转换成 Java 编程语言的字节码。这很好，除了……我们不在 Android 设备上运行那个字节码。相反，我们运行一个叫做 DEX 的东西，它代表 Dalvik 可执行文件。Dalvik 是 Android 上最初的运行时的名字。目前的运行时，自 Android 5.0 Lollipop 以来，被称为 ART，代表 Android 运行时…但 ART 仍然运行相同的 DEX 代码(如果用不处理旧可执行文件的东西来替换运行时，那就太傻了，不是吗？)

## #2 D8

D8 是这个链中的第二个编译器，从 Java 字节码翻译成 DEX 代码。此时，您已经有了可以在 Android 上运行的代码。或者，你可以选择使用第三个编译器*，称为 [R8](https://developer.android.com/studio/build/shrink-code) 。*

## #3 R8(可选，但推荐)

[R8](https://developer.android.com/studio/build/shrink-code) 是 D8 的扩展模式，用于优化和收缩应用程序。基本上，它是 ProGuard 的替代品。因为 R8 作为 D8 编译步骤的一部分运行，所以它能够比 ProGuard 更快，ProGuard 是一个完全独立的编译器。

但是 R8 在默认情况下是不启用的，所以如果您想使用它(对于这里讨论的一些优化，您可以使用它)，您需要启用它。在应用程序的 build.gradle 文件中设置`minifyEnabled = true`来强制 R8 运行。这将发生在所有其他编译之后，以确保您得到一个缩小和优化的应用程序。

```
android {
    buildTypes {
        release {
            minifyEnabled true proguardFiles getDefaultProguardFile(
                ‘proguard-android-optimize.txt’),
                ‘proguard-rules.pro’
        }
    }
}
```

# 枚举

现在，我们来谈谈枚举。

不管你是用 Java 还是 Kotlin 代码编写，枚举的开销和功能本质上是一样的，但是有趣的是，一旦我们将 R8 引入其中，我们可以对其中的一些开销做些什么。

枚举本身没有任何不明显的开销。如果你在 Kotlin 中使用一个 enum，那么它将被翻译成 Java 编程语言中的 enum。这没什么大不了的。(是的，我们曾经谈论过避免枚举…但那是很多年前的事了，而且是在整个运行时之前——枚举是好的)。

当您使用 when 语句时，开销就会增加。

首先，让我们看一个示例枚举:

```
enum class BlendMode {
    OPAQUE,
    TRANSPARENT,
    FADE,
    ADD
}
```

这里我们的枚举有四个值。那些是什么并不重要；这只是一个例子。

# 枚举+ When

现在让我们添加一个 when 语句，打开该枚举:

```
fun blend(b: BlendMode) {
    when (b) {
        BlendMode.OPAQUE -> src()
        BlendMode.TRANSPARENT -> srcOver()
        BlendMode.FADE -> srcOver()
        BlendMode.ADD -> add()
    }
}
```

对于每个枚举值，我们将调用另一个函数。

如果你看看它在 Java 字节码中编译成了什么(你可以在 Android Studio 中直接查看字节码(工具-> Kotlin ->显示 Kotlin 字节码)，然后点击“反编译”按钮)，它看起来就像这样:

```
public static void blend(@NotNull BlendMode b) {
    switch (BlendingKt$WhenMappings.
            $EnumSwitchMapping$0[b.ordinal()]) {
        case 1: {
            src();
            break;
        }
        // ...
    }
}
```

它调用此数组，而不是直接对枚举值进行切换。那个数组是从哪里来的？

数组被保存在这个生成的类文件中。那个类文件是从哪里来的？

这是怎么回事？

# 自动生成的枚举映射

事实证明，出于二进制兼容性的原因，代码不能简单地打开枚举的序数值，因为这是脆弱的。你可以有一个带有枚举的库，如果你改变了枚举中这些项的顺序，你可以破坏别人的应用程序，即使它在代码中看起来是一样的，它们只是顺序不同而已。但是是订单的实现细节导致了破坏。

因此，相反，编译器接受序数值并将其映射到另一个值，然后无论您对这些枚举做什么，用该库构建的代码仍将运行。

当然，这意味着某些东西会以你的名义产生。或者在这种情况下，多个东西。

生成的代码如下所示:

```
public final class BlendingKt$WhenMappings {
    public static final int[] $EnumSwitchMapping$0 =
            new int[BlendMode.values().length];

    static {
        $EnumSwitchMapping$0[BlendMode.OPAQUE.ordinal()] = 1;
        $EnumSwitchMapping$0[BlendMode.TRANSPARENT.ordinal()] = 2;
        $EnumSwitchMapping$0[BlendMode.FADE.ordinal()] = 3;
        $EnumSwitchMapping$0[BlendMode.ADD.ordinal()] = 4;
    }
}
```

生成了一个类 BlendingKt$WhenMappings。其中包含一个数组$EnumSwitchMapping$0，其中存放了映射，然后是一些实际执行映射操作的静态代码。

只有一个 when 语句时就是这种情况。如果我们有更多的 when 语句，我们将为这些语句中的每一个都有另一个数组，即使它们使用相同的枚举。

所有这些开销没什么大不了的。但这确实意味着，在您不知道的情况下，一个类正在以您的名义生成，然后该类中的一些数组，所有这些都需要在类加载和实例化时花费更多的时间。

幸运的是，我们可以做些事情来减少开销:这就是 R8 的用武之地。

# R8 来了

R8 是一个有趣的优化器。它可以看到你应用程序中的所有东西。它能看到所有的代码，不管是你写的代码还是你正在编译的库代码。例如，它知道可以避免这些枚举映射的开销，就可以做出关于优化的决策。它不需要映射信息，因为它知道代码只会以这种特定的方式使用这些枚举，所以它可以调用序数值。

这是 R8 优化代码反编译后的样子:

```
public static void blend(@NotNull BlendMode b) {
    switch (b.ordinal()) {
        case 0: {
            src();
            break;
        }
        // ...
    }
}
```

它避免了生成类和映射数组，只是创建了您想要的最佳代码。

看看 R8，看看科特林，享受用科特林编写更好的应用程序。

# 欲了解更多信息

有关这些主题的更多信息，请访问以下链接:

[](https://developer.android.com/studio/command-line/d8) [## d8 |安卓开发者

### d8 是一个命令行工具，Android Studio 和 Android Gradle 插件使用它来编译项目的 Java 字节码…

developer.android.com](https://developer.android.com/studio/command-line/d8) [](https://developer.android.com/studio/build/shrink-code) [## 缩小、混淆和优化您的应用| Android 开发者

### 为了让你的应用程序尽可能的小，你应该在你的发布版本中启用收缩来移除未使用的代码和…

developer.android.com](https://developer.android.com/studio/build/shrink-code) [](https://jakewharton.com/blog/) [## 邮件

### 博客文章、演示文稿、GitHub 等等。

jakewharton.com](https://jakewharton.com/blog/) 

[杰克·沃顿](https://medium.com/u/8ddd94878165?source=post_page-----3f8f314c0a13--------------------------------)详细介绍了 d8 和 r8 的工作原理，给出了各种特性的具体例子，以及如何直接运行编译器和如何获得反编译结果。

这篇文章(和它的视频版本)摘自我和[罗曼·盖伊](https://medium.com/u/c967b7e51f8b?source=post_page-----3f8f314c0a13--------------------------------)在 KotlinConf 2019 上做的一个更长的演示。查看视频，了解关于 Kotlin、语言特性和优化的更多信息。