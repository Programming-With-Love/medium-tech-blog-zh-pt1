# 科特林的一个可选的地方

> 原文：<https://medium.com/square-corner-blog/an-optionals-place-in-kotlin-17d7b271eefe?source=collection_archive---------0----------------------->

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

随着可空性成为 Kotlin 类型系统中的一等公民，对一个`Optional`类型的需求几乎消失了。但是，仅仅因为您可以显式地表示可空性，并不意味着总是允许 null。

例如，retreate 为 RxJava 1.x 和 2.x 提供了适配器，允许将您的请求建模为单元素流。

```
interface MyApiService {
  @GET("/api/user/settings")
  fun userSettings(): Observable<Settings>
}
```

RxJava 2 与 RxJava 1 的不同之处在于，它不允许流中包含 null。如果我们使用 RxJava 2，并且`Settings`的转换器返回 null，将会发生异常。因此，为了表示这个流中没有响应体的值，我们需要一个像`Optional`这样的抽象。

[改型 2.3.0](https://github.com/square/retrofit/blob/master/CHANGELOG.md#version-230-2017-05-13) 引入了两个新的*委托*转换器，用于 Guava 和 Java 8 中的`Optional`类型。与 Moshi、Gson 和 Wire 等其他库转换器不同，这些新转换器的不同之处在于它们并不真正将字节转换为对象。相反，它们委托其他转换器来处理字节，然后将可能为空的结果包装成一个`Optional`。

```
val retrofit = Retrofit.Builder()
    .baseUrl("https://example.com")
    .addConverterFactory(Java8OptionalConverterFactory.create())
    .addConverterFactory(WireConverterFactory.create())
    .addCallAdapter(RxJava2CallAdapterFactory.create())
    .build()
```

通过在序列化转换器旁边添加一个`Optional`转换器，其主体可以反序列化为 null 的请求可以改为返回一个`Optional`。

```
interface MyApiService {
  @GET("/api/user/settings")
  fun userSettings(): Observable<**Optional<**Settings**>**>
}
```

今天的[改进 2.3.0](https://github.com/square/retrofit/blob/master/CHANGELOG.md#version-230-2017-05-13) 版本包含了与我们最近的 Okio 和 OkHttp 版本相同的针对 Java 和 Kotlin 显式空性的 JSR 305 注释。正如我们在上面所看到的，仅仅拥有这些注释或者一个可以模拟可空性的类型系统有时是不够的。对于这些情况，在 Java 和 Kotlin 中都一样，使用`Optional`有它的位置。

这篇文章是 Square 的“ [Square 开源♥s·科特林](/square-corner-blog/square-open-source-loves-kotlin-c57c21710a17)”系列文章的一部分。