# 在 JAVA 和 Kotlin 的互操作中使用可空性注释

> 原文：<https://medium.com/quick-code/the-case-of-npe-in-interop-between-java-and-kotlin-8790b2b73e18?source=collection_archive---------6----------------------->

Kotlin 在最近几年越来越受欢迎，现在在 Android 开发社区非常流行。一年前我开始研究 Kotlin，和大多数程序员一样，我很敬畏。这是迈向前端开发新时代的正确且几乎完全的一步。

当想到 Kotlin 时，人们脑海中浮现的第一个优点当然是零安全特性。它很好，但也有一些陷阱需要注意，尤其是当你在一个应用程序代码库中同时拥有 java 和 kotlin 的时候。在这篇文章中，我将解释其中的一些，让你意识到它可能带来的负面影响。

如果您认为 NullPointerException 已经消失，请耐心等待。这里有一个 NPE 的例子:

假设你有一个 Java 方法，在某些情况下可能会返回`null`。看看下面的代码示例。

```
public String getMyString(boolean giveMeNull) {
     return giveMeNull ? null : “GOOD”;
}
```

看起来很无辜？如果试图从 Kotlin 源代码中调用这个方法作为函数会发生什么？如果你用 true 作为参数调用这个函数，你会得到一个异常。为什么？？

Kotlin 编译器依赖注释`@Nullable`来决定返回类型或参数类型。因为我们的 Java 方法返回类型上没有`@Nullable`注释，Kotlin 认为返回类型是不可空的，这就是为什么它会出错。

这对一些 android 库方法和类也有不好的影响。我找到的其中一个就是`SpannableString` 级。考虑下面的示例代码:

```
val mainString: String? = nullval spannableString = SpannableString(mainString)
```

在这种情况下，你也会获得 NPE 奖。android.text 包中的`SpannableString` 构造函数在参数上没有定义`@Nullablle` 注释，如果提供的参数是`null`，它会抛出一个 NPE，原因很明显。现在，如果这是从 java 源调用的，你可能通过确保参数不为空来捕捉空值，但是因为在 Kotlin 中你没有意识到空值检查，你可能会落入这个陷阱。

好了，就这样..这些看起来很小的问题，你甚至可能不会遇到，但是记住它们绝对是安全的。安全编码！！再见。

有关更多信息，请查看官方文档[https://kot linlang . org/docs/reference/Java-interop . html # nullability-annotations](https://kotlinlang.org/docs/reference/java-interop.html#nullability-annotations)