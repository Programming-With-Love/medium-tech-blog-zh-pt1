# Kotlin 揭秘:何时使用自定义访问器

> 原文：<https://medium.com/androiddevelopers/kotlin-demystified-when-to-use-custom-accessors-939a6e998899?source=collection_archive---------8----------------------->

![](img/7ade95072317bc03cffd81b4449f43d4.png)

Photo by [Barn Images](https://unsplash.com/photos/t5YUoHW6zRo?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/toolbox?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

在处理 [UAMP](https://github.com/googlesamples/android-UniversalMusicPlayer) 时，我发现出于各种原因，将几个支持库类包装在一个助手中很方便。我想从这个[包装器](https://github.com/googlesamples/android-UniversalMusicPlayer/blob/7e4820893ff26c15f4d11cf90e2a168e06f1e60f/app/src/main/java/com/example/android/uamp/MediaSessionConnection.kt)中得到的一个特性是快速引用“传输控制”类的能力，该类用于发出播放和暂停等命令。

为了方便起见，我在包装器中添加了一个属性:

```
val transportControls: MediaControllerCompat.TransportControls
    = mediaController.transportControls
```

需要记住的是，`mediaController`是一个`lateinit var`，因为它是在回调服务连接时创建的。因此，当应用程序运行时，这段代码会立即崩溃:

```
E/AndroidRuntime: FATAL EXCEPTION: main
    Process: com.example.android.uamp.next, PID: 18641
    java.lang.RuntimeException: Unable to start activity ComponentInfo{com.example.android.uamp.next/com.example.android.uamp.MainActivity}:
    kotlin.UninitializedPropertyAccessException: lateinit property mediaController has not been initialized
```

[修复方法](https://github.com/googlesamples/android-UniversalMusicPlayer/blob/7e4820893ff26c15f4d11cf90e2a168e06f1e60f/app/src/main/java/com/example/android/uamp/MediaSessionConnection.kt#L59-L60)是向该属性添加一个自定义访问器:

```
val transportControls: MediaControllerCompat.TransportControls
    get() = mediaController.transportControls
```

但是为什么呢？

理解发生了什么的最简单的方法是查看这两种变体的字节码。在 Android Studio 中这样做就像**工具> Kotlin >显示 Kotlin 字节码**一样简单。然后选择**反编译**按钮查看等价的 Java 代码。

没有`get()`:

```
@NotNull
private final TransportControls transportControls = mediaController.getTransportControls();@NotNull
public final TransportControls getTransportControls() {
    return this.transportControls;
}
```

用`get()`:

```
@NotNull
public final TransportControls getTransportControls() {
    return mediaController.getTransportControls();
}
```

在第一种情况下，当构造类时，一个成员变量`transportControls`正在被设置。在第二个示例中，它实际上在每次访问`transportControls`属性时都会调用`mediaController.getTransportControls()`。

对我来说，这正是我想做的。我不想把从`mediaController.getTransportControls()`返回的对象保存在我的对象里，我只是想让写那个代码更方便。正因为如此，代号应该是`val transportControls get() = ...`。

然而，这并不总是正确的方法。以这段代码为例:

```
val ticket get() = findTicketInDb()
```

呀！每次访问`ticket`时，代码都会在数据库中搜索车票！这可能不是我们想要的。在这种情况下，正确的方法应该是这样写:

```
val ticket = findTicketInDb()
```

这将在创建对象时调用`findTicketInDb()` *一次*，并在每次访问属性`ticket`时返回相同的值。

科特林属性可能看似简单。理解创建对象时计算一次的属性和使用自定义`get()`方法的属性(每次访问属性时计算一次)之间的区别，可能意味着编写清晰的代码和将非预期的行为引入应用程序之间的区别。

请务必关注 [Android 开发者](https://medium.com/androiddevelopers)出版物，获取更多精彩内容，并关注更多关于 Kotlin 的文章！