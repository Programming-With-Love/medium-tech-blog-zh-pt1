# Kotlin 是 JSON 的绝佳语言

> 原文：<https://medium.com/square-corner-blog/kotlins-a-great-language-for-json-fcd6ef99256b?source=collection_archive---------0----------------------->

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

虽然 JSON 有它的缺点，但我真的很喜欢它。它易于阅读，解析速度很快，并且非常简单。下面是来自 [GitHub 的范例 API](https://developer.github.com/v3/issues/) 的一个示例消息:

```
{
  "url": "https://api.github.com/repos/square/okio/issues/156",
  "id": 91393390,
  "number": 156,
  "title": "ByteString CharSequence idea",
  "state": "open",
  "created_at": "2015-06-27T00:49:40.000Z",
  "body": "Let's make CharSequence that's backed by bytes.\n"
}
```

Kotlin 简洁的不可变数据类使得为这个 JSON 构建一个基本模型变得很容易。

```
data class Issue(
    val url: String,
    val id: Long,
    val number: Long,
    val title: String,
    val state: String,
    val created_at: String,
    val body: String)
```

就是这样。没有`equals()`、`hashCode()`或`toString()`样板文件。我们甚至不需要建造者！让我们扩展模型以利用 Kotlin 的默认值和显式空值:

```
data class Issue(
    val url: String,
    val id: Long,
    val number: Long,
    val title: String,
    **val comments: Long = 0L**,
    val created_at: String,
    **val closed_at: String?,**
    **val body: String = ""**)
```

默认值填补了从网络解码 JSON 时的空白。我喜欢在我的测试用例中创建样本数据时，我可以把它们省略掉。显式可空类型可以防止数据问题。

今天我们将通过`moshi-kotlin`模块发布 [Moshi 1.5](https://github.com/square/moshi/blob/master/CHANGELOG.md) 和强大的 Kotlin 支持。Moshi 的类型适配器和注释将 JSON 绑定到一个惯用的数据模型。

```
data class Issue(
    val url: String,
    val id: Long,
    val number: Long,
    val title: String,
    **val state: IssueState**,
    val comments: Long = 0L,
    **@Json(name = "created_at") val createdAt: Date**,
    **@Json(name = "closed_at") val closedAt: Date?**,
    val body: String = "")
```

对于问题的状态和时间戳，此类使用正确的类型而不是字符串。`@Json`注释将 JSON 中的`snake_case`名称映射到 Kotlin 中的`camelCase`属性名称。

为了设置这个，我需要一个`Moshi.Builder`和一个`JsonAdapter`。我可以使用 Kotlin 的原始字符串在代码中嵌入一个示例消息。

```
val issueJson = """
{
  "url": "[https://api.github.com/repos/square/okio/issues/156](https://api.github.com/repos/square/okio/issues/156)",
  "id": 91393390,
  "number": 156,
  "title": "ByteString CharSequence idea",
  "state": "open",
  "created_at": "2015-06-27T00:49:40.000Z"
}
"""val moshi = Moshi.Builder()
    .add(KotlinJsonAdapterFactory())
    .add(Date::class.java, Rfc3339DateJsonAdapter().nullSafe())
    .build()val issueAdapter = moshi.adapter(Issue::class.java)
val issue = issueAdapter.fromJson(issueJson)
```

如果你正在使用 JSON， [Moshi](https://github.com/square/moshi) 和 [Kotlin](https://kotlinlang.org) 帮助你用更少的代码构建更好的模型。注意`moshi-kotlin`使用`kotlin-reflect`进行属性绑定。按照 Android 的标准，这种依赖性很大(1.7 MiB / 11，500 个方法)。我们正在考虑创造性的方法来解决这个问题！

这篇文章是 Square 的“ [Square 开源♥s·科特林](/square-corner-blog/square-open-source-loves-kotlin-c57c21710a17)”系列文章的一部分。