# OkHttp 的新 URL 类

> 原文：<https://medium.com/square-corner-blog/okhttps-new-url-class-515460eea661?source=collection_archive---------1----------------------->

## Java URLs 很痛苦。HttpURL 是来帮忙的。

*由* [撰写*杰西·威尔逊*T5*。*](https://medium.com/u/dee2b4f5bec4?source=post_page-----515460eea661--------------------------------)

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

Android 和 Java 开发人员在处理 URL 时有几种选择:

*   [Java . net . URL](http://developer.android.com/reference/java/net/URL.html)20 岁，显示年龄。它可以工作，但是存在一些实现上的问题。
*   [java.net.URI](http://developer.android.com/reference/java/net/URI.html) 太严格了，因为它拒绝了像[这个](http://maps.googleapis.com/maps/api/staticmap?center=Brooklyn+Bridge,New+York,NY&zoom=13&size=370x250&maptype=roadmap%20&markers=color:blue|label:S|40.702147,-74.015794&markers=color:green|label:G|40.711614,-74.012318%20&markers=color:red|color:red|label:C|40.718217,-73.998284&sensor=false)这样的真实世界的 URL，也太宽松了，因为它接受了像“”这样的部分 URL。
*   [android.net.Uri](http://developer.android.com/reference/android/net/Uri.html) 是乐观的:它不验证它的输入。这在某些情况下节省了不必要的工作，但在其他情况下限制了它的效用。

在 [OkHttp](http://square.github.io/okhttp/) 中，我们一直使用 java.net.URL 作为我们的首选模型。这是可行的，但是很麻烦；每种方法几乎都是正确的，但不完全正确。因此，我们依靠助手方法和变通方法来获得我们想要的行为。例如，这是我们获取 URL 端口的代码:

```
**public** **static** **int** **getEffectivePort(**URL url**)** **{**
  **int** specifiedPort **=** url**.**getPort**();**
  **return** specifiedPort **!=** **-**1
      **?** specifiedPort
      **:** getDefaultPort**(**url**.**getProtocol**());**
**}****public** **static** **int** **getDefaultPort(**String protocol**)** **{**
  **if** **(**"http"**.**equals**(**protocol**))** **return** 80**;**
  **if** **(**"https"**.**equals**(**protocol**))** **return** 443**;**
  **return** **-**1**;**
**}**
```

我们厌倦了变通办法。所以在 OkHttp 2.4 中，我们创建了自己的 URL 模型， [HttpUrl](http://square.github.io/okhttp/javadoc/com/squareup/okhttp/HttpUrl.html) 。它在四个重要方面改进了它的前辈。

# 1.解析 URL

作为一名 Java 程序员，最糟糕的是什么？捕捉不可能抛出的异常:

```
**public** URL **makeYouCry()** **{**
  **try** **{**
    **return** **new** **URL(**"https://youtube.com/watch?v=dQw4w9WgXcQ"**);**
  **}** **catch** **(**MalformedURLException e**)** **{**
    **throw** **new** **AssertionError(**"say goodbye"**);**
  **}**
**}**
```

我们新的 HttpUrl 类不会强迫您处理 malformedurexception or urisynctaxexception。相反，parse()只是在不理解您传递给它的内容时返回 null。没有异常意味着我们终于可以声明 URL 常量了:

```
**public** **static** **final** HttpUrl ANDROID_DOWNLOAD_URL **=** HttpUrl**.**parse**(**
    "https://play.google.com/store/apps/details?id=com.squareup.cash"**);**
```

解析器足够严格，只能生成格式良好的 URL，但对原始用户输入足够宽松。它适合在浏览器的地址栏中使用，并与主流 web 浏览器中的 URL 解析器保持一致。

# 2.规范化 URL

让我们解析一些 URL，将它们添加到一个集合中，并打印结果。这是了解 equals()是如何实现的一个简单方法。

```
Set**<**URL**>** set **=** **new** LinkedHashSet**<>();**
set**.**add**(new** **URL(**"http://Square.GitHub.io/"**));**
set**.**add**(new** **URL(**"http://square.github.io:80/"**));**
set**.**add**(new** **URL(**"http://google.github.io/"**));**
System**.**out**.**println**(**set**);**
```

示例中的前两个 URL 在语义上是相等的:它们具有相同的主机名(主机名不区分大小写)和相同的端口(因为 80 是 HTTP 的默认端口)。第三个网址不同。

但是 java.net.URL 对三个网址都是一视同仁的，因为 square.github.io 和 google.github.io 托管在同一个 IP 地址！上面的程序打印了以下内容:

```
[http://Square.GitHub.io/]
```

恶！调用 URL.equals()还会进行 DNS 查找，这对性能和正确性都不利。这是一个长期存在的问题，奇怪的是二十年后它仍然没有被修复。

URI 和 Uri 都没有规范化它们的输入。因此，尽管前两个 URL 在语义上是等价的，但它们并不相等。对于这两个模型，程序打印:

```
[http://Square.GitHub.io/, [http://square.github.io:80/,](http://square.github.io:80/,) [http://google.github.io/]](http://google.github.io/])
```

使用 HttpUrl，我们对输入 Url 进行轻度规范化。它打印两个值:

```
[http://square.github.io/, [http://google.github.io/]](http://google.github.io/])
```

一个好的 equals()方法意味着 HttpUrl 适合用作 LinkedHashMap 甚至是 [Guava 缓存](http://docs.guava-libraries.googlecode.com/git/javadoc/com/google/common/cache/Cache.html)中的一个键。

# 3.查询参数

Java 的内置 URL 类缺乏从 URL 中提取查询参数的能力。假设您有这样一个用于 Twitter 搜索的 URL:

```
[https://twitter.com/search?q=cute%20%23puppies&f=images](https://twitter.com/search?q=cute%20%23puppies&f=images)
```

调用 getQuery()或 getRawQuery()会返回一个字符串，其中所有参数都粘在一起:

```
q=cute%20%23puppies&f=images
```

这很尴尬。幸运的是，HttpUrl 可以轻松地分解查询:

```
HttpUrl url **=** HttpUrl**.**parse**(**
    "https://twitter.com/search?q=cute%20%23puppies&f=images"**);**
System**.**out**.**println**(**url**.**queryParameter**(**"q"**));**
System**.**out**.**println**(**url**.**queryParameter**(**"f"**));**
```

queryParameter()方法提取请求的值并解码。上面的代码打印如下:

```
cute #puppies
images
```

还有一种不用解码就可以检索查询参数的方法(如果你喜欢这类东西的话)。

# 4.HttpUrl。建设者

正如 HttpUrl 类允许您将 Url 分解成它的方案、主机、路径、查询和片段，它的构建器可以从原始材料中构建 URL。

```
HttpUrl url **=** **new** HttpUrl**.**Builder**()**
    **.**scheme**(**"https"**)**
    **.**host**(**"www.google.com"**)**
    **.**addPathSegment**(**"search"**)**
    **.**addQueryParameter**(**"q"**,** "polar bears"**)**
    **.**build**();**
```

构建器接受语义或编码形式的每个组件。它不会对百分比字符进行双重编码，也不会将加号误解为空格。构建器还使基于现有 URL 的构建变得容易:

```
**public** **static** **final** HttpUrl APP_DETAILS_URL **=**
    HttpUrl**.**parse**(**"https://play.google.com/store/apps/details"**);****public** HttpUrl **playStoreUrl(**String appId**)** **{**
  **return** APP_DETAILS_URL**.**newBuilder**()**
      **.**setQueryParameter**(**"id"**,** appId**)**
      **.**build**();**
**}**
```

# 在 OkHttp 上获取

在 GitHub 上获得 [OkHttp 2.4.0。它有你在 Java 和 Android 应用程序中进行 HTTP 请求所需要的东西。](https://github.com/square/okhttp)

[](/@swankjesse) [## 杰西·威尔逊

### 安卓和笑话。

medium.com](/@swankjesse)