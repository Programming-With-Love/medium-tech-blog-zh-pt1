# HttpMocker:一个简单的 HTTP 模仿库，供 Kotlin 处理离线模式

> 原文：<https://blog.kotlin-academy.com/httpmock-my-first-oss-library-5bae8adbccf4?source=collection_archive---------1----------------------->

![](img/ca4d4e6450c5c5421813938dbbdf5e72.png)

我们的世界越来越紧密相连，我们在手机上使用的应用程序很好地证明了这一点。我们在智能手机上运行的大多数应用程序都依赖远程呼叫来为我们的用户提供优质服务。问题是，即使我们的无线连接有所改善，我们的带宽增加了，我们仍然经常面临网络让我们失望的情况:飞行模式，覆盖范围很差的偏远农村地区，甚至是意想不到地结束你烦人电话的臭名昭著的隧道。

那么当你的应用失去联系远程服务器的能力时会发生什么呢？它会显示错误信息还是无限加载屏幕？它会冻结，或者更糟，崩溃吗？在开发新功能时，这些情况通常被认为是角落情况，但即使在 2019 年，失去连接仍然是一件事。如果用户想看看你的应用程序是什么样的，但是还没有一个可以展示你所做的伟大工作的有趣数据的帐户，该怎么办？或者您的开发人员在服务准备好之前就开始实现功能，并且在他们的大部分任务中停滞不前？

好吧，所有这些问题都不一定容易解决，可能没有一个简单的银弹库，但是对于其中一些情况，有一个简单的解决方法:模仿 HTTP 连接。出于开发目的，您可以设置一个假服务器，用预定义的模拟场景进行响应。实现起来并不困难，并且可以给前端开发人员一些喘息的空间，让他们在等待实际的服务器上线时继续编码。

但这并不能解决你在手机上使用该应用程序的情况，手机根本不能访问任何服务器，甚至不能访问你的模拟服务器。我以前见过这样的情况，当我和我的团队试图在 sprint 结束时演示我们的工作，却发现我们被困在一个房间里，实际上无法访问公司的私有网络。在这些情况下，你需要一个可以打包在应用程序中的解决方案，这样设备就可以自给自足，至少在你的演示完成之前是如此。

该解决方案必须与我们的业务逻辑分离，这样我们就可以轻松地从实际连接的数据切换到模拟状态，而不是在网络不可访问时使用“if”语句来加载虚假数据，从而膨胀我们的代码。因为我们的项目，像今天的大多数 Android 应用程序一样，使用著名的 [OkHttp](https://square.github.io/okhttp/) 栈进行 Http 调用，这看起来像是插入该逻辑的一个可能的切入点。

这就是我如何基于 OkHttp 的拦截器设计了一个解决方案。并将该解决方案捆绑到我刚刚发布的开源库中: [HttpMocker](https://github.com/speekha/httpmocker) 。原理相当简单:通过添加一个拦截器，您可以在不改变业务逻辑的情况下阻止网络调用并提供虚假响应。从应用程序其余部分的角度来看，您的代码永远不会知道网络调用是实际执行的还是被嘲笑的。

# 快速入门

那么我们如何使用这个图书馆呢？ [HttpMocker](https://github.com/speekha/httpmocker) 是一个用 Kotlin 编写的轻量级库，它提供了一个 OkHttp 拦截器来停止网络调用，并从场景文件或内存规则中返回一个固定响应。你所需要做的就是给你的 OkHttp 客户端添加一个`MockResponseInterceptor`。下面是一个使用动态模拟的最小配置示例:

```
val interceptor = MockResponseInterceptor.Builder()
    .useDynamicMocks{
        ResponseDescriptor(code = 200, body = "Fake response body")
    }
    .setInterceptorStatus(ENABLED)
    .build()val client = OkHttpClient.Builder()
    .addInterceptor(interceptor)
    .build()
```

如果您的拦截器被禁用，它将不会干扰实际的网络调用。如果启用了它，它将需要找到模拟 HTTP 调用的场景。动态模拟意味着您必须以编程方式为每个请求提供响应，这允许您定义有状态的响应(相同的调用可能会根据用户在这些调用之间所做的事情产生不同的响应):

```
val callback = object : RequestCallback {
    var count = 0 override fun loadResponse(request: Request) =
        ResponseDescriptor(body = "Fake response body ${count++}")
    }
}
```

您可以通过实现`RequestCallback`接口来计算响应，或者简单地提供一个 lambda 函数来进行计算，并将模拟的响应作为`ResponseDescriptor`返回(这是一个简单的数据类，描述了答案的所有细节:HTTP 响应代码、头、主体等。).

另一种选择是使用静态模拟。静态模拟是存储为静态文件的场景。下面是一个使用静态模拟的 Android 应用程序示例:

```
val interceptor = MockResponseInterceptor.Builder()
        .parseScenariosWith(mapper)
        .loadFileWith { context.assets.open(it) }
        .setInterceptorStatus(ENABLED)
        .build()
```

在本例中，我们决定将场景存储在应用程序的 assets 文件夹中，因此我们提供了一个 lambda 函数，使用`context.assets.open()`来加载文件。如果您不在 Android 应用程序中，您也可以将它们作为资源放在您的类路径中，并使用`Classloader`来访问它们，或者甚至将它们存储在某个文件夹中，并使用您熟悉的任何文件 API 来访问该文件夹。

另外，您需要提供一个`Mapper`来解析 JSON 场景文件。理论上，场景不必存储为 JSON:如果您喜欢，可以使用 XML 或任何其他格式，**，只要您提供自己的映射器类**来序列化和反序列化业务对象。

```
val mapper = object : Mapper {
    override fun deserialize(payload: String): List<Matcher> = ...
    override fun serialize(matchers: List<Matcher>): String = ...
}
```

就这个库而言，虽然有一些现成的映射器，但它们目前只处理 JSON 格式，并且基于 Jackson、Gson、Moshi 和 Kotlinx 序列化。它们在特定的模块中提供，因此您可以基于您已经使用的 JSON 库选择一个，从而限制在您的应用程序中为相同目的服务的重复库的风险。基于不使用任何依赖项的定制 JSON 解析器的实现也是可用的。

除了这些强制设置之外，还有一些可选设置可以调整。

一个`FilePolicy`定义了检查哪个文件来找到一个请求的匹配。默认选项镜像 URL 路径(您的场景必须存储在与它们寻址的 URL 相匹配的文件夹中)。库中提供了一些其他策略，但是您也可以定义自己的策略。

```
val interceptor = MockResponseInterceptor.Builder()
        .parseScenariosWith(mapper)
        .decodeScenarioPathWith(filingPolicy)
        .loadFileWith { context.assets.open(it) }
        .setInterceptorStatus(ENABLED)
        .build()
```

还可以定义一个假的网络延迟:由于模拟响应比实际的 HTTP 调用快得多，为了保持应用程序的原始行为，您可能希望在处理它们时包含一些任意的延迟。例如，您可以使用这个参数来确保您的加载屏幕显示出来，而不是被跳过。该参数是一个常规设置，但是在这些场景中可以根据每个请求进行覆盖。

```
val interceptor = MockResponseInterceptor.Builder()
        .parseScenariosWith(mapper)
        .loadFileWith { context.assets.open(it) }
        .setInterceptorStatus(ENABLED)
        .addFakeNetworkDelay(300L)
        .build()
```

最后，这个拦截器支持几种不同的模式:禁用、启用、混合或记录。禁用时，它不会干扰实际的网络呼叫，并让它们通过。启用后，它将停止所有请求，并根据预定义的场景回答这些请求。如果选择混合模式，预定义场景无法响应的请求将被实际执行。因此得名:响应可以来自场景文件，也可以来自实际的 HTTP 调用。几个拦截器甚至可以堆叠起来处理不同的情况(参见[的测试，一个例子是](https://github.com/speekha/httpmocker/blob/master/tests/src/test/kotlin/fr/speekha/httpmocker/StaticMockTests.kt))。

最后，拦截器还有一个记录模式。此模式允许您在不干扰您的请求的情况下记录场景。如果您选择此模式来生成您的方案，您将必须提供一个存储方案的根文件夹。

```
val interceptor = MockResponseInterceptor.Builder()
        .parseScenariosWith(mapper)
        .loadFileWith { context.assets.open(it) }
        .setInterceptorStatus(RECORD)
        .saveScenariosIn(File(rootFolder))
        .build()
```

此外，您应该意识到所有的请求和响应属性都将被记录下来。一般来说，您会想要检查产生的场景，并手动清理它们。例如，每个请求将被记录其 HTTP 方法、路径、每个头或参数、其主体。如果您正在记录呼叫以便在将来使用它们，那么所有这些细节对您来说可能并不重要:也许您所关心的只是 URL 和方法，在这种情况下，您可以手动删除所有多余的标准。另一方面，在记录模式下使用带有`SingleFilePolicy`的`MockResponseInterceptor`可以方便地将所有网络调用和响应记录在一个文件中，这样您就可以提取它们用于以后的调试目的(虽然只是在开发和测试期间，但不要在生产中这样做！).

# 构建静态场景

用静态模拟回答请求分两步完成:

*   首先，如果拦截器被启用(或者处于混合模式)，它将尝试计算一个文件名，其中应该存储适当的场景。根据您选择的归档策略，这些文件可以以许多不同的方式进行组织:都在同一个文件夹中，在与 URL 路径匹配的单独文件夹中，忽略或不忽略服务器主机名。
*   第二，一旦找到文件，就会加载它的内容，并且必须找到更精确的匹配。场景文件包含一个“T2”列表，即请求模式和相应响应的列表。根据它试图回答的请求，拦截器将扫描所有的请求声明，一旦发现一个符合情况的声明就停止。

当编写一个请求模式时，包含的元素应该在匹配的请求中找到。元素越多，匹配就越精确。元素越少，匹配越宽松。一个请求甚至可以完全省略(在这种情况下，所有请求都匹配)。例如:

*   当指定一个方法时，匹配的请求必须使用相同的 HTTP 方法。
*   当指定查询参数时，匹配请求必须至少有**个**所有这些参数(但可以有更多)。
*   指定标题时，匹配请求必须至少有**个**个相同的标题(但可以有更多)。

以下是一个 JSON 形式的场景示例:

```
[
  {
    "request": {
      "method": "post",
      "headers": {
        "myHeader": "myHeaderValue"
      },
      "params": {
        "myParam": "myParamValue"
      },
      "body": ".*1.*"
    },
    "response": {
      "delay": 50,
      "code": 200,
      "media-type": "application/json",
      "headers": {
        "myHeader": "headerValue1"
      },
      "body-file": "body_content.txt"
    }
  }, {
       "response": {
         "delay": 50,
         "code": 200,
         "media-type": "application/json",
         "headers": {
           "myHeader": "headerValue2"
         },
         "body": "No body here"
       }
  }
]
```

在本例中，相应 URL 上的 POST 请求(包括值为`myParamValue`的查询参数`myParam`、值为`myHeaderValue`的头`myHeader`和包含数字“1”的主体(基于用作主体的正则表达式)将与第一种情况相匹配:它将得到类型为`application/json`的 HTTP 200 响应，头`myHeader`的值为`headerValue1`。这个响应的主体可以在附近的文件名`body_content.txt`中找到。在任何其他情况下，请求将由第二个响应来响应:一个 HTTP 200 响应，以`headerValue2`作为头，一个简单的字符串`No body here`作为体。

# 好了

只需几行代码，这个库就允许您拦截网络调用并提供模拟响应。作为开发人员，我们的团队喜欢在开发或演示期间离线运行应用程序。

因此，请查看[演示应用](https://github.com/speekha/httpmocker/tree/master/demo)或亲自尝试一下，并让我知道您的想法！

**http mocker**:[https://github.com/speekha/httpmocker](https://github.com/speekha/httpmocker)

# 单击👏说“谢谢！”并帮助他人找到这篇文章。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察 Twitter](https://twitter.com/ktdotacademy) 并在 medium 上关注我们。

如果你需要一个科特林工作室，看看我们如何能帮助你: [kt.academy](https://www.kt.academy/) 。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)