# 使用 Riemann 和 Clojure 进行性能监控

> 原文：<https://medium.com/walmartglobaltech/performance-monitoring-with-riemann-and-clojure-eafc07fcd375?source=collection_archive---------2----------------------->

![](img/25f21712959076fbaea4ee8de4c4bcbb.png)

Photo credit: [katkaZV](https://pixabay.com/en/shell-sea-underwater-world-beach-1680755/)

在 [@WalmartLabs](http://twitter.com/WalmartLabs) 的 eReceipts 和杂货数据服务团队负责在基于 Clojure 的技术栈上构建、维护和操作关键的后端数据服务。沃尔玛的商店和在线顾客依赖我们的服务来提供平稳、可靠和快速的购物体验，我们已经将正常运行时间和性能作为我们构建的每个系统的基本考虑因素。我们的工程师团队非常依赖[黎曼](http://riemann.io)来监控我们系统的内部状态，并帮助我们发现、诊断和潜在升级或干预性能下降的情况，以免它们演变为影响客户的停机。

# 什么是黎曼？

Riemann 是一个低延迟的事件流处理系统，因此它的核心领域是离散事件和事件流。这种方法不同于一些传统的监控系统，因为服务器和静态基础设施是紧急关注点，而不是基本关注点，这使得它非常适合动态、高度分布式的环境。

# 它是如何工作的？

Riemann 通过 UDP 或 TCP 以协议缓冲区的形式接收事件。在我们的例子中，这些事件是来自应用程序的结构化日志条目，或者是由其他活动监控系统生成的特定事件。一旦接收到这些事件，Riemann 将它们传递到事件“流”中，事件“流”是配置的一部分，负责过滤、聚集和处理它们。

黎曼配置实际上只是一个 Clojure 程序，其中大部分由事件流组成。事件流是使用 Riemann `[streams](http://riemann.io/api/riemann.config.html#var-streams)`函数配置的，该函数需要传递一个参数函数，每个接收到的事件都会调用这个函数。Riemann 文档用顶级`streams`函数发出的事件“流”来描述这一点。

例如，这里有一个非常简单的黎曼配置:

```
(defn my-event-handler
 [event]
 (println “Handling event:” event))(streams my-event-handler)
```

因为配置只是代码，事件流只是函数，所以 Riemann 实际上是无限灵活的。它提供了一个丰富的可组合函数库，这些函数库组成了一个流处理 DSL，使您无需掌握 Clojure 的全面知识就能构建复杂的监控场景。从哲学上讲，这使得黎曼更像一个框架或库，而不是常规的监控软件。事件只是任意的结构(除了少数标准的、惯用的字段之外)，所以事件的语义几乎完全取决于流的实现，而不是事件本身所表示的数据。这意味着黎曼本身不会对事件做任何事；完全由流配置来处理。

举一个更复杂的例子，如果您想要创建一个黎曼配置来监视错误事件，并最多每 60 秒发送一封电子邮件，您可以这样做:

```
(def email (mailer {:host “mail.relay”
                    :from “[riemann@example.com](mailto:riemann@example.com)”}))(streams
  (where (and (= (:service event) “my-service”)
              (= (:level event) “ERROR”))
    (rollup 1 60
      (email “[ops@example.com](mailto:ops@example.com)”))))
```

在这个例子中，`where`、`rollup`和`mailer`都是由黎曼流 DSL 提供的。`where`是一个 Clojure 宏，它返回一个函数，该函数将根据提供的逻辑谓词过滤出事件，然后将这些事件传递给`rollup`函数，后者返回一个函数，该函数将累积事件并将它们传递给由`email`创建的流，最多每 60 秒一次。`email`是一个返回函数，该函数将一个或多个事件格式化成电子邮件通知，并将其发送到提供的地址。

诚然，这是一大堆“函数返回函数，函数返回函数”，但最终我们只需要考虑两个基本概念:事件和流，或者用 Clojure 的术语来说，映射和函数。这种简单性和灵活性是 Riemann 最吸引人的地方之一，它允许我们在现有架构的限制下构建复杂的监控。

# 黎曼中的性能度量

在设计任何业务关键型系统的初始活动中，一个重要的部分是彻底理解性能和正常运行时间的需求，这最终必须用可测量的术语来定义。这些需求通常以目标的形式出现，比如“web 服务响应时间的第 99 个百分点应该少于 100 毫秒”，或者“所有 HTTP 请求的 95%应该是成功的”为了衡量我们在这些目标下的性能，我们在 Riemann 配置中构建了一个声明性性能阈值 DSL。它允许我们通过配置建立阈值，并直接从已经流经 Riemann 的事件中获取度量。

在我们的 DSL 中，我们考虑两种不同的基本类型的性能指标:`percentiles`和`histograms`。百分比适用于数值测量值(例如响应时间)，可以在任意点定义，而直方图适用于离散值(例如 HTTP 响应状态代码)，并采用百分比或计数器阈值的形式。直方图和百分位数都是通过一个`interval`定义的，它是离散时间窗口内的秒数，在该时间窗口内事件将被一起考虑。

举个例子，给定类似这样的事件:

```
{:service "query-service response"
 :metric 85
 :status-code “200”}
```

我们在单独的 [EDN](https://github.com/edn-format/edn) 配置文件中表示性能目标阈值:

```
{:percentiles {"query-service response" {0.99 {:<= 100 
                                               :interval 600}}}
 :histograms {"query-service response" 
              {:status-code {"^2[0–9]{2}$" {:>= 0.95
                                           :interval 600
                                           :type :percentage}
                             ".*" {:> 0
                                   :interval 300
                                   :type :count}}}}}
```

在这个片段中，我们跟踪 3 个不同的性能阈值:

1.  在 10 分钟的时间间隔内，第 99 个百分位数的响应时间应小于或等于 100。用于比较的值将与事件上的`:metric`键相关联(这是与数字相关联的事件的黎曼习语)。
2.  在 10 分钟的时间间隔内，95%的带有`:status-code`的响应事件应该具有 2XX 的状态代码。
3.  在 5 分钟内，所有带有`:status-code`的响应事件的计数应大于零。

我们的黎曼配置将上述阈值配置图转换为一组流，这些流过滤相关事件、累加值并将其与预期阈值进行比较。当给定阈值的状态从可接受变为不可接受，或者再次变为不可接受时，流会将更改事件传递给通知流，通知流会在我们的外部警报系统中发送或清除警报。

我们的配置从 EDN 阈值配置文件中生成这些流，但是上述阈值之一的静态(当然也是幼稚的)版本可能如下所示:

```
(percentiles 600    ;; interval
             [0.99] ;; percentile
             (smap (fn [event]
                     (assoc
                      event
                      :state (if (<= (:metric event) 100)
                               "ok"
                               "violation")))
                   (changed :state {:init "ok"}
                            (where (= (:state event) "ok")
                                   notify-restored
                                   (else
                                    notify-violated)))))
```

在本例中，我们使用了 Riemann DSL 提供的四个函数/宏:

1.  `percentiles` —聚合 600 秒间隔内的事件，并将具有第 99 百分位的`:metric`值的事件传递到下一个流，在本例中为`smap`。
2.  `smap` —这是一个流映射，这意味着它将作为第一个参数提供的函数映射到每个事件，然后将其传递给下一个流。在这种情况下，下一个流是`changed`。
    映射函数为事件`:state`属性赋值，如果事件的`:metric`值小于或等于 100，则该值取决于`"ok"`或`"violation",`。由于该流位于`percentile`流之下，它将只接收代表由`percentile`处理的任何事件的第 99 个百分位度量值的事件。
3.  `changed` —该流过滤掉每个事件，除了那些指定属性(在本例中为`:state`)已经从它看到的最后一个事件改变的事件。`{:init “ok"}`参数只是一个初始化`changed`流状态的选项映射。这允许我们仅在某个方向超过性能指标阈值时发送通知，并防止在状态为`"ok"`时为第一个事件发送通知。
4.  `where` —这个流简单地调用基于状态的适当的`notify-*`流，在我们设计的例子中，假设这些流是在配置中的其他地方定义的函数。

# 摘要

最后，我们喜欢黎曼，因为它不碍事，让我们可以集中精力决定*跟踪什么*，而不是*如何*跟踪它。然后，最大的挑战变成了确定反映稳定和标称操作值的指标、阈值和间隔，以确保阈值违规产生的信号的纯度和可用性。我们的团队为我们的系统定义了阈值，以便它们代表系统健康状况的强有力指标，并且我们将它们与我们的主动警报系统相集成，以自动管理事件，并在这些值落入和超出范围时通知待命工程师。这为我们提供了一个框架，可以顺利地自动化我们的错误检测试探法，而不需要在传统的监控集成方面进行大量投资，并且它将我们从检查仪表板和从上游或外部来源获得问题通知的恶性循环中解放出来。