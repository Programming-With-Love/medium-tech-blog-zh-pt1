# Kotlin 的匕首:陷阱和优化

> 原文：<https://medium.com/androiddevelopers/dagger-in-kotlin-gotchas-and-optimizations-7446d8dfd7dc?source=collection_archive---------1----------------------->

![](img/c61db2768a4f123bc454778bbce7005a.png)

Illustration by [Virginia Poltrack](https://twitter.com/vpoltrack)

[Dagger](https://dagger.dev/) 是 Android 中常用的一个流行的依赖注入框架。它提供了完全静态和编译时的依赖，解决了许多基于反射的解决方案的开发和性能问题。

这个月，一个新的[教程](https://dagger.dev/tutorial/)发布了，以帮助你更好地理解它是如何工作的。本文重点介绍如何将 **Dagger 与 Kotlin** 结合使用，包括优化构建时间的**最佳实践**以及您可能遇到的**陷阱**。

Dagger 是使用 Java 的注释模型实现的，Kotlin 中的注释并不总是与如何编写等价的 Java 代码直接并行。这篇文章将重点介绍它们的不同之处，以及如何在 Kotlin 上使用 Dagger 而不会头痛。

这篇文章的灵感来自于这个 [Dagger 问题](https://github.com/google/dagger/issues/900)中的一些建议，这个问题讨论了 Kotlin 中 Dagger 的最佳实践和痛点。感谢所有在那里发表评论的贡献者！

# kapt 构建改进

为了改善你的构建时间，Dagger 在 [v2.18](https://github.com/google/dagger/releases/tag/dagger-2.18) 中增加了对 **gradle 的增量注释处理**的支持！这在 Dagger [v2.24](https://github.com/google/dagger/releases/tag/dagger-2.24) 中默认启用。如果您正在使用一个较低的版本，如果您想从中受益，您需要添加几行代码(如下所示)。

另外，您可以告诉 Dagger 不要格式化生成的代码。这个选项是在 Dagger [v2.18](https://github.com/google/dagger/releases/tag/dagger-2.18) 中添加的，它是 [v2.23](https://github.com/google/dagger/releases/tag/dagger-2.23) 中的默认行为(不生成格式化代码)。如果您使用的是较低版本，请禁用代码格式以缩短构建时间(参见下面的代码)。

在您的`build.gradle`文件中包含这些编译器参数，以使 Dagger 在构建时更有性能:

或者，如果您使用 Kotlin DSL 脚本文件，将它们包含在使用 Dagger 的模块的`build.gradle.kts`文件中，如下所示:

# 字段属性的限定符

当在 Kotlin 的属性上放置注释时，不清楚 Java 是否会在属性的*字段*或属性的*方法*上看到注释。在注释上设置前缀`field:`确保限定符在正确的位置结束([更多细节见文档](https://kotlinlang.org/docs/reference/annotations.html#annotation-use-site-targets))。

✅对注入字段应用限定符的方法是:

```
@Inject **@field:MinimumBalance** lateinit var minimumBalance: BigDecimal
```

❌反对:

```
@Inject @MinimumBalance lateinit var minimumBalance: BigDecimal 
**// @MinimumBalance is ignored!**
```

如果 Dagger 图中存在该类型的不合格实例，忘记添加`field:`可能会导致注入错误的对象。

**10/28/19**更新:这个问题在 Dagger v2.25 中得到修复。如果你使用这个版本，你可以只键入以前不起作用的内容:

```
@Inject @MinimumBalance lateinit var minimumBalance: BigDecimal 
**// FIXED: @MinimumBalance is NOT ignored!**
```

# Static @提供函数优化

如果`@Provides`方法是`static`，Dagger 生成的代码会更有性能。为了在 Kotlin 中实现这一点，使用 Kotlin `object`而不是`class`，并用`@JvmStatic`注释您的方法。这是一个 ***最佳实践*** ，你应该尽可能地遵循。

如果您需要一个抽象方法，您需要将`@JvmStatic`方法添加到一个伴随对象中，并用`@Module`对其进行注释。

或者，您可以将目标模块提取出来，并将其包含在抽象模块中:

**更新 10/28/19** :有了 Dagger v2.25.2，就不需要用`@JvmStatic`标记`@Provides`函数了。匕首会正确理解的。

# 注入仿制药

Kotlin 编译带有通配符的泛型，以使 Kotlin APIs 与 Java 一起工作。当一个类型作为参数([更多信息在这里](https://kotlinlang.org/docs/reference/java-to-kotlin-interop.html#variant-generics))或作为字段出现时，就会产生这些。例如，一个 Kotlin `List<Foo>`参数在 Java 中显示为`List<? super Foo>`。

这导致了 Dagger 的问题，因为它期望精确的(又名[不变的](https://en.wikipedia.org/wiki/Class_invariant))类型匹配。使用`@[JvmSuppressWildcards](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.jvm/-jvm-suppress-wildcards/index.html)`将确保 Dagger 看到没有通配符的类型。

当您使用 [Dagger 的多绑定特性](https://dagger.dev/multibindings.html)注入集合时，这是一个常见的问题，例如:

```
class MyVMFactory @Inject constructor(
  private val vmMap: Map<String, **@JvmSuppressWildcards** Provider<ViewModel>>
) { 
    ... 
}
```

# 内联方法体

Dagger 通过检查返回类型来确定由`@Provides`方法配置的类型。在 Kotlin 函数中指定返回类型是可选的，甚至 IDE 有时也鼓励您重构代码，使内联方法体隐藏返回类型声明。

如果推断的类型不同于您想要的类型，这可能会导致错误。让我们看一些例子:

如果您想要将特定类型添加到图形中，内联会按预期工作。查看在 Kotlin 中做同样事情的不同方式:

如果您想要提供接口的实现，那么您必须显式指定返回类型。不这样做可能会导致问题和错误:

Dagger 通常在 Kotlin 开箱后使用。然而，您必须注意一些事情，以确保您正在做您真正想做的事情:字段属性上的限定符的`@field:`，内联方法体，以及注入集合时的`@JvmSuppressWildcards`。

Dagger 优化是免费的，添加它们并遵循最佳实践来改进您的构建时间:启用增量注释处理，禁用格式化并在 Dagger 模块中使用静态`@Provides`方法。