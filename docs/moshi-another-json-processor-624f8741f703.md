# Moshi，另一个 JSON 处理器

> 原文：<https://medium.com/square-corner-blog/moshi-another-json-processor-624f8741f703?source=collection_archive---------0----------------------->

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

程序员很少会两次处理同一个问题。要么第一个足够好，你就完成了，要么第一个不够好，你愚蠢地再次尝试。环境和傲慢给了我两次尝试的机会。

# Guice，一个依赖注入器

当我 2012 年开始在 Square 工作时，Android 团队正在使用 [Google Guice](https://github.com/google/guice) 进行依赖注入。我为 Guice 做了贡献，所以在新工作中使用熟悉的代码感觉很好。但是有一个问题:Guice 对于移动设备来说太慢了。即使是最新的姜饼设备也不够快。

# Dagger，另一个依赖注入器

我建议通过从基于反射的 Guice 切换到我们创建的新的基于 codegen 的依赖注入器来解决这个问题。我开始了将成为[匕首](https://github.com/google/dagger)的原型。第二次构建东西要容易得多！我知道哪些功能是重要的，哪些可以省略。Dagger 1.0 借用了 Guice 的核心特性，让它们变得更加简单快捷。

谷歌后来接管了 Dagger 的开发。他们扩展了它的 API，提高了它的性能。

# JSON 处理器 Gson

我还参与了 Google Gson T8，Square 用它来进行 JSON 编码和数据绑定。和 Guice 一样，使用我曾经工作过并引以为豪的东西也不错。Gson 快速、安全且易于上手。它有一个灵活的数据绑定模型，可以扩展到复杂的用例。

但是我用 Gson 越多，一些老错误就越让我恼火。例如，Gson 的默认日期格式被破坏:

```
Date epoch = new Date(0);
String epochJson = new Gson().toJson(epoch);
// "Dec 31, 1969 7:00:00 PM"
```

编码的日期没有时区，因此当编码器和解码器不共享时区时，数据会损坏。这个 bug 令人沮丧的部分是，修复它也很危险:我们不知道什么应用程序期待当前的格式，我们也不知道如果我们改变它，什么会被破坏。(Gson 将*默认读取* [RFC 3339](https://www.ietf.org/rfc/rfc3339.txt) 类似`1970–01–01T00:00:00Z`的日期)。

另一个问题是图书馆如何应对不良输入。当你使用`Gson.fromJson()`将一个字符串转换成 JSON 时，它总是宽松的。它认为`{a=TRUE}`等同于`{"a":true}`。对于无效输入，如空字符串和 null，它通常也会返回。这隐藏了错误，但修复它可能会破坏现有的应用程序。

# 介绍 Moshi

18 个月前[杰克](https://github.com/jakewharton/)、[斯科特](https://github.com/dragonsinth)和我创建了[魔石](https://github.com/square/moshi)，试图借用 Gson 的一些核心理念，让它们变得更简单、更快捷。我们想要一个小巧、高效、默认安全的库。

Moshi 试图在编码时防止错误，并可以在解码时检测错误。例如，如果您将一个`java.util.UUID`字段添加到您的一个模型对象中，Moshi 将会报错，除非您为该类型显式注册一个适配器。这样，您就不会无意中将 JDK 实现的细节泄露到您的 JSON 数据中。

```
IllegalArgumentException: Platform class java.util.UUID requires explicit JsonAdapter to be registered
  at com.squareup.moshi.ClassJsonAdapter$1.create()
  at com.squareup.moshi.Moshi.adapter()
```

类似地，Moshi 有一个严格的模式，在未使用的字段上失败。对于集成测试来说，这是一个很好的选择！

```
JsonDataException: Cannot skip unexpected STRING at $.fullNane
  at com.squareup.moshi.JsonUtf8Reader.skipValue()
  at com.squareup.moshi.ClassJsonAdapter.fromJson()
  at com.squareup.moshi.JsonAdapter.fromJson()
```

我们一直在悄悄地完善 Moshi 的功能集，提高它的性能。在最近的 1.4 版本中，我们增加了对 JSON 值的支持，可以将对象转换成字节流或内存模型。

如果你正在构建一个新的应用并且需要 JSON，请考虑 [Moshi](https://github.com/square/moshi) 。准备好了。