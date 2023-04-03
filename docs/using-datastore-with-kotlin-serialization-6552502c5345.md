# 通过 Kotlin 序列化使用数据存储

> 原文：<https://medium.com/androiddevelopers/using-datastore-with-kotlin-serialization-6552502c5345?source=collection_archive---------2----------------------->

![](img/09501006a753581e9bc2c13ec2344f18.png)

到目前为止，我们[已经分享了](https://android-developers.googleblog.com/2020/09/prefer-storing-data-with-jetpack.html)如何使用 [Protos](https://developers.google.com/protocol-buffers/docs/overview) 或 [Preferences](https://developer.android.com/reference/kotlin/androidx/datastore/preferences/core/package-summary) 的数据存储。在幕后，两个数据存储版本都使用 Protos 来序列化数据。您还可以通过使用 [Kotlin 序列化](https://kotlinlang.org/docs/reference/serialization.html)来使用带有自定义数据类的数据存储。这有助于减少样板代码，而不必学习或依赖 Protobuf 库，同时仍然为您的数据提供模式。

你需要做几件事:

*   定义数据类
*   **确保你的数据类是不可变的**
*   使用 Kotlin 序列化实现数据存储序列化程序
*   开始使用它

# 定义数据类

Kotlin [数据类](https://kotlinlang.org/docs/reference/data-classes.html)非常适合用于数据存储，因为它们可以与 Kotlin 序列化无缝协作。数据存储依赖于为数据类自动生成的`equals`和`hashCode` 。数据类还生成对调试和更新数据有用的`toString`和`copy`函数

# 确保你的数据类是不可变的

确保你的类是不可变的非常重要，因为**数据存储与可变类型**不兼容。对 DataStore 使用可变类型将导致难以捕捉的错误和竞争情况。数据类不一定是不可变的。

变量是可变的，所以应该使用变量:

数组是可变的，所以你不应该暴露它们。

即使我们使用只读列表作为数据类的成员，它仍然是可变的。相反，你应该考虑使用[不可变/持久集合](https://github.com/Kotlin/kotlinx.collections.immutable):

使用可变类型作为数据类的成员使其可变。相反，您应该确保所有成员都是不可变类型。

# 实现您的数据存储序列化程序

Kotlin 序列化[支持多种格式](https://kotlinlang.org/docs/reference/serialization.html#formats)包括 JSON 和协议缓冲区。我将在这里使用 JSON，因为它非常常见，易于使用，并且以明文形式存储，便于调试。Protobuf 也是一个不错的选择，因为它更小，更快，并且兼容 [protobuf-lite。](https://developer.android.com/codelabs/android-proto-datastore)

为了使用 Kotlin 序列化来读写 JSON 中的数据类，需要用`@Serializable`注释数据类，并使用`Json.decodeFromString<YourType>(string)`和`Json.encodeToString(data)`。这里有一个关于`UserPreferences`的例子:

⚠️ Parcelables 与 DataStore 一起使用不安全，因为不同 Android 版本之间的数据格式可能会有所不同。

# 使用序列化程序

在构建数据存储时，将您创建的序列化程序传递到数据存储中:

读取数据看起来与使用 protos 一样:

您可以使用生成的`.copy()`函数来更新数据:

# 结论

使用带有 Kotlin 序列化和数据类的 DataStore 可以减少 boiler plate 并有助于简化您的代码，但是，您必须小心不要通过可变性引入错误。您需要做的就是定义您的数据类并实现序列化程序。你自己试试吧！

要了解更多关于数据存储的信息，请查看我们的[文档](https://developer.android.com/topic/libraries/architecture/datastore)，并获得一些关于我们的[原型数据存储](https://developer.android.com/codelabs/android-proto-datastore#0)和[首选项数据存储](https://developer.android.com/codelabs/android-preferences-datastore#0) codelabs 的实践经验。