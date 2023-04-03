# 代码走出一家意大利面酒吧…

> 原文：<https://medium.com/capital-one-tech/code-walked-out-of-a-spaghetti-bar-2dcc1750789a?source=collection_archive---------1----------------------->

## 通过利用数据收集器和日志聚合器，使用工具来理清信息

![](img/f8593b88d6a31e57bc16cd1d776de66f.png)

我认为我们都可以说吃意大利面比处理意大利面代码更好。因此，让我们来处理一个有趣的例子，说明仪器如何帮助你解决这种情况。

🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝

想象一个开发人员编写一个在线意大利面购物车应用程序。以下代码行可用于记录成功的订单:

```
LOGGER.info("The spaghetti order was successfully placed");
```

或者这个记录异常的例子怎么样:

```
...
catch(SomeException ex) {
  LOGGER.error(“Something broke the meatball!!!”, ex);
....
```

这就是我喜欢称之为*的东西，记录下来，然后忘掉它。*这里的好消息是开发人员正在记录…不太好的消息是他们能否回答以下问题:

*   *过去 45 分钟内下了多少份意大利面订单(而且他们不能查询生产数据库)？*
*   *在过去的 24 小时内，肉丸被打碎了多少次？*

🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝

尽管上面描述的场景可能很琐碎，但它通过数据收集器和日志聚合器的力量强调了在您的软件应用程序中插装的重要性。

# 什么是仪器仪表？

在他们的文档中， [Oracle 将工具](https://docs.oracle.com/javase/7/docs/api/java/lang/instrument/Instrumentation.html)定义如下:

> *“检测是将字节码添加到方法中，目的是收集工具要使用的数据。由于这些变化纯粹是附加的，所以这些工具不会修改应用程序的状态或行为。这种良性工具的例子包括监控代理、分析器、覆盖率分析器和事件记录器。”*

如上所述，仪器可以以多种方式应用。本文将关注事件记录器的用例，包括行业内的一些最佳实践。

# 从前在一个遗留系统上

随着微服务架构的流行，很可能在某个时间点，您会被要求将一个功能从遗留系统抽象到微服务中。如果您的新微服务依赖于其他下游微服务来完全执行其功能，那么您可能会对如何处理这些下游服务的监控和警报有疑问。他们大概是这样的:

*   *“如果下游服务 A、B、C、D 和 E 出现故障，或者只是 A、C 和 E 出现故障，我们是否应该发出警报？”*
*   *“如果任何下游服务中断 x 时间，我们是否应该得到警告？”*
*   *“这些下游服务多久停机一次？”*
*   *“这个微服务在 x 时间内处理了多少个成功的呼叫？”*

听起来很熟悉，对吧？也许您已经使用了状态设计模式和排队基础设施，因此您的新微服务能够实现稳定性，而不管下游服务的状态如何。然而，这并不能否认这样一个事实，即即使微服务是稳定的，如果任何下游服务中断，它也无法执行其功能。鉴于这是微服务架构的本质，您将需要构建服务来解决这些已知的场景。

插装不应该干扰代码的行为，而是无缝地从中提取信息。接下来是实现这一点的一些步骤。

# 日志事件数据模型

您的日志事件数据模型是一个构造块，它确保您捕获代码中发生的不同事件的重要信息。它为您的日志事件提供了数据收集器可以轻松解析的结构。我们使用了一个基于层次的模型，其中父类拥有我们用例中每个日志事件应该拥有的所有公共信息。

回到我们最喜欢的意大利面条店，强调这是如何实现的:

```
public class EventLogRecord {
    private final String appName = AppConstants.APP_NAME;
    private final String logMessage;
    private final EventLogType eventLogType; public EventLogRecord (EventLogType eventLogType, String logMessage) {
        this.eventLogType = eventLogType;
       this.logMessage = logMessage;
    }// getters 
...
}
```

每个事件日志都与一个枚举类型相关联:

```
public enum EventLogType {
    DownStreamServiceCalled,
    ExceptionHappened,
    SpaghettiOrder,
    GeneralLogging
}
```

任何日志事件都可以从父类继承，然后实现它计划支持的任何字段。在我们的示例中，意大利面订单日志事件将具有如下数据模型:

```
public class SpaghettiOrderEventLogRecord extends EventLogRecord {
	private final boolean orderSuccessful;
	public SpaghettiOrderEventLogRecord(EventLogType eventLogType, String logMessage, boolean orderSuccessful) { super(eventLogType, logMessage);
		this.orderSuccessful = orderSuccessful;
	} // getters
}
```

下游服务调用的日志事件如下所示:

```
public class DownStreamServiceEventLogRecord extends EventLogRecord {
	private final Integer httpStatus; public DownStreamServiceEventLogRecord(EventLogType eventLogType, String logMessage, Integer httpStatus) { super(eventLogType, logMessage);
		this.httpStatus = httpStatus;
	} // getters
}
```

那么异常日志事件将如下所示:

```
public class ExceptionEventLogRecord extends EventLogRecord {
	private final String stackTrace;
	public ExceptionEventLogRecord(EventLogType eventLogType, String logMessage, String stackTrace) { super(eventLogType, logMessage);
		this.stackTrace = stackTrace;
	} // getters
}
```

上面的数据模型只是为了突出本文示例的简单情况。在现实世界的应用程序中，将使用工厂方法来创建这些数据模型。在记录这些事件时，像 [gson](https://github.com/google/gson) 或 [jackson mapper](https://github.com/FasterXML/jackson) 这样的 JSON 映射器可以用来记录事件，以便数据收集器解析对象。

```
...
LOGGER.info(gson.toJson(eventLogRecord));
...
```

# 什么是数据收集器？

数据收集器用于解析日志文件，并将它们发送到 NoSQL 数据库、流服务或日志聚合器。 [Fluentd](https://www.fluentd.org/) 和 [Logstash](https://www.elastic.co/logstash) 是解析日志文件的数据收集器的例子。两者能力相当，但是 Fluentd 有更多插件，如果你不想完全使用 ELK stack，它仍然兼容 Elasticsearch。

有很多种方法可以解析你的日志数据，而 [grok 模式](https://grokdebug.herokuapp.com/patterns)是最流行的方法之一。Grok 模式本质上是一个抽象的正则表达式，用于在日志中查找任何指定的模式。使用我们虚构的意大利面示例，下面是一个成功的意大利面订单的日志输出:

```
2020-04-09 08:13:30.736  INFO 11532 --- [nio-8443-exec-6] e.t.n.e.d.l.a.impl.DownStreamClass     : {"appName":"Spag-R-Us", "logMessage":"Spaghetti order was successful", "eventLogType":"SpaghettiOrder", "orderSuccessful": true}
```

然后，数据收集器可以对日志输出应用以下 *grok 模式*:

```
%{TIMESTAMP_ISO8601:logDate}. %{LOGLEVEL:LogLevel}%{DATA:className} : %{GREEDYDATA:logMessageBody}
```

在应用 grok 模式后，日志输出被转换为:

```
{
  "logDate": [
    "2020-04-09 08:13:30.736"
  ],
  "LogLevel": [
    "INFO"
  ],
  "className": [
    " 11532 --- [nio-8443-exec-6] e.t.n.e.d.l.a.impl.DownStreamClass    "
  ],
  "logMessageBody": [
    "{"appName":"Spag-R-Us", "logMessage":"Spaghetti order was successful", "eventLogType":"SpaghettiOrder", "orderSuccessful": true}"
  ]
}
```

最后，***logMessageBody****对象由数据收集器解析为 *JSON* 对象，然后传递给日志聚合器:*

```
*{
	"appName":"Spag-R-Us",
	"logMessage":"Spaghetti order was successful",
	"eventLogType":"SpaghettiOrder",
	"orderSuccessful":true
}*
```

*此时，我们已经有了可量化的数据，日志聚合器可以使用这些数据进行指标计算和进一步分析。*

# *什么是日志聚合器？*

*日志聚合器是一种软件工具，它收集整个平台的日志，并将它们存储在一个集中的位置，用于各种目的，包括警报、故障排除甚至机器学习。 [Elasticsearch](https://www.elastic.co/webinars/introduction-elk-stack) 和 [Graylog](https://www.graylog.org/) 是企业使用的开源日志聚合软件的例子。在我团队的工作中，我们使用 *Elasticsearch* 进行日志聚合。基于我们使用的日志事件数据模型，我们能够编写 [Elasticsearch 查询](https://www.elastic.co/guide/en/elasticsearch/reference/current/query-dsl-query-string-query.html)，用于仪表板显示和监控警报。*

*所有这些都让我们回到了意大利面条场景中的两个问题*

*   **过去 45 分钟内下了多少份意大利面订单(而且他们无法查询生产数据库)？**
*   *在过去的 24 小时内，肉丸被打碎了多少次？*

*第一个问题很容易回答，在日志聚合器中设计一个仪表板，使用类似下面的查询(每个日志聚合器的实现会有所不同):*

```
*COUNT("eventLogType":"SpaghettiOrder" AND "orderSuccessful": true)*
```

*所有日志聚合器都有一个可调的时间跨度，它将产生在指定时间跨度内发生的结果。在我们的示例中，将为过去 45 分钟内的任何事件设置 timespan。这使得查询在任何指定的时间段内都是动态的，并且易于查看。*

*第二个问题也有非常相似的解决方案。可以使用以下查询，(每个日志聚合器的实现会有所不同):*

```
*COUNT("eventLogType":"ExceptionHappened" AND "logMessage": “Something broke the meatball!!!”)*
```

*就像第一个问题一样，您可以动态地应用一个 timespan 变量，在这种情况下，它将被设置为 24 小时。此外，大多数日志聚合器支持查询可视化，如饼图和条形图，它们在日志到来时提供实时更新。*

# *把它转回来*

*测井仪器中涉及的要点可总结如下:*

*   *构建一个数据模型，对您希望在应用程序中发生的各种事件进行分类。*
*   *配置您的数据收集器来解析您的日志并提取您设计的数据模型。*
*   *配置日志聚合器以从数据收集器接收数据。*
*   *分析、构建仪表板和旋转。*

*就像奶奶做的意大利面条一样，使用仪器的秘方是实践和亲眼目睹。这可能感觉像是额外的编码，但是当您能够在不改变一行代码的情况下提供有价值的信息时，这种好处是值得的。*

*🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝🍝*

**最初发表于*[*【https://www.capitalone.com】*](https://www.capitalone.com/tech/software-engineering/spaghetti-code-instrumentation/)*。**

**披露声明:2020 资本一。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。**