# Apache Camel 的企业集成模式

> 原文：<https://medium.com/quick-code/enterprise-integration-patterns-with-apache-camel-b8ed42967576?source=collection_archive---------3----------------------->

![](img/e4b398018058e00d3af131dc28702738.png)

Apache Camel 是一个集成框架。那是什么意思？假设您正在处理一个项目，该项目使用 Kafka 和 RabbitMQ 中的数据，读写各种数据库，转换数据，将所有内容记录到文件中，并将处理后的数据输出到另一个 Kafka 主题。您还必须实现服务的错误处理(重试、死信通道等)。)让一切完美运行。好像很难。

Apache Camel 帮助您集成许多组件，如数据库、文件、代理等等，同时保持简单性并促进企业集成模式。让我们看一些基于集成模式的例子。你可以在[这个资源库](https://github.com/diogodanielsoaresferreira/apache_camel_demo)中找到代码。

你好。我想告诉你一个伟大的开源工具，它很棒，却没有得到应有的爱: [**阿帕奇骆驼**](https://camel.apache.org/) 。

Apache Camel 是一个集成框架。那是什么意思？假设您正在处理一个项目，该项目使用 Kafka 和 RabbitMQ 中的数据，读写各种数据库，转换数据，将所有内容记录到文件中，并将处理后的数据输出到另一个 Kafka 主题。您还必须实现服务的错误处理(重试、死信通道等)。)让一切完美运行。好像很难。

Apache Camel 帮助您集成许多组件，如数据库、文件、代理等等，同时保持简单性并促进企业集成模式。让我们看一些基于集成模式的例子。你可以在[这个库](https://github.com/diogodanielsoaresferreira/apache_camel_demo)中找到代码。

我们将利用 [**事件驱动的消费者**](https://www.enterpriseintegrationpatterns.com/patterns/messaging/EventDrivenConsumer.html) 模式，从一个 Kafka 主题消费事件并输出到另一个主题。事件将是用户发送的文本消息的表示。

Example of event schema that will be received from Kafka

Java code using Camel to read from topic and output message to another topic

差不多就是这样！我们还添加了日志，以便在日志中查看消息正文。log 参数使用简单语言传递，这是一种用于计算表达式的 Apache Camel 语言。

现在让我们实现一个 [**消息过滤器**](https://www.enterpriseintegrationpatterns.com/patterns/messaging/Filter.html) 。此模式过滤掉不符合特定条件的邮件。在我们的例子中，我们将只处理类型为“chat”的那些。

Camel route to filter out messages of type other than “chat”

很简单，对吧？我们现在将消息从 JSON 解组到 UserMessage POJO，以便能够按类型过滤。在发送到另一个 Kafka 主题之前，我们在 JSON 中再次封送。

UserMessage POJO

现在假设我们想将所有消息存储在一个文件中。此外，对于发送者是“John Doe”的消息，我们希望将它们存储在一个不同的文件中，以便进行测试。为此，我们可以使用 [**基于内容的路由器**](https://www.enterpriseintegrationpatterns.com/patterns/messaging/ContentBasedRouter.html) 模式。

After sending the message to the broker, it will now also store it in a file, depending on the emitter

如果文件已经存在，我们将追加事件并在每个事件的末尾添加一个新行。对于其他发射器，我们将做同样的事情，但是将它们存储在另一个文件中。它看起来确实像一个“如果”结构，对吗？

我们可以在事件中看到一个“设备”列表，我们希望逐个记录它们。我们如何做到这一点？使用 [**拆分器**](https://www.enterpriseintegrationpatterns.com/patterns/messaging/Sequencer.html) 模式，我们可以遍历任何列表。我们可以按顺序或平行地做。在这个例子中，让我们试着按顺序来做。

Before sending the message to the output topic, we will iterate through each device and log it

我们可以用任何可迭代的域来分割。如您所见，我们再次使用简单的语言来访问事件的内容。

让我们试试更难的。我们从不同的发射器接收带有文本的消息，但是我们想要聚合多个文本消息，并为一个发射器创建一个包含所有消息的新消息。为此，我们可以使用 [**聚合器**](https://www.enterpriseintegrationpatterns.com/patterns/messaging/Aggregator.html) 模式。聚合器模式允许缓冲事件并等待其他事件。当接收到另一个事件时，可以根据我们的需要执行自定义聚合。满足条件时，会发送新事件。该条件可以基于收到的事件数量、超时或任何其他自定义条件。

在我们的例子中，我们将创建一个新的 POJO，它将聚合来自发射器的文本消息。新事件将在发射器收到第一个事件 5 秒后发送。

Aggregation to create a new event with the messages of an emitter

我们使用内存聚合，但是也可以使用其他数据存储，比如 Postgres 或 Redis。我们使用简单的语言来聚合消息的发送者，并且我们创建了一个定制的聚合策略，如下所示。

在自定义聚合策略中，对于第一个事件(oldExchange==null)，我们使用消息文本创建一个新的 CombinedUserMessage。对于所有其他事件，我们将消息的文本添加到组合事件中。

Custom aggregation strategy

这一切都很棒，但是我们如何将变换应用于一个领域呢？我们现在有了一个组合事件，但最棒的是我们可以通过某种方式处理组合事件，并通过组合文本消息的多个元素将其转换为纯文本。我们可以使用 [**消息转换器**](https://www.enterpriseintegrationpatterns.com/patterns/messaging/MessageTranslator.html) 模式来实现。

We can call bean() to apply a transformation to our message

Java Bean to apply transformations to a message

我们可以直接从 Camel Route 调用 bean 函数，并使用普通的 Java 代码执行我们需要的所有转换。整洁！

我们可以看到我们的骆驼路线正在变大。例如，如果我们想要将它们在文件之间分开，我们该怎么做呢？两个内存组件允许我们这样做:**直接**和 **SEDA** 。

**Direct** 是一个同步端点，其工作方式类似于从一个路由到另一个路由的调用。让我们用它来分离在一个文件中存储事件的路由。

We separate our main route into two routes, connected with the Direct endpoint

太好了！还有一个内存组件对我们有用: **SEDA** 。SEDA 的工作方式类似于 Direct，但它是异步的，这意味着它将消息放在一个队列中，供其他线程处理。让我们用 SEDA 来把接收卡夫卡的信息和消费它的途径分离开来。

With the SEDA endpoints, we now have only one consumer from Kafka instead of two

现在我们的路线简单多了。假设我们需要执行一个周期性的任务，比如清理。我们可以利用**定时器**端点。让我们通过创建每 5 秒运行一次的路线来举例说明。

Simple timer that is activated every 5 seconds

既然我们的应用程序几乎已经可以生产了，我们必须提高容错能力。如果由于某种原因，消息在路由中出错，会发生什么？让我们实现 [**死信**](https://www.enterpriseintegrationpatterns.com/patterns/messaging/DeadLetterChannel.html) 模式。当路由出现错误时，消息被发送到另一个 Kafka 主题，以便稍后可以重新处理。

Configure a default error handler for all routes, using the dead letter pattern to send messages to a kafka topic

就是这样！错误处理程序配置适用于该类中的所有路由。我们将原始消息发送到主题(在路由中第一次收到的那个)。我们还可以配置重试策略、超时和其他常见的容错配置，但是因为我们不需要它，所以我们将保持原样。

既然我们已经到了本文的结尾，我还想向您展示一些东西:将 **REST** 端点配置为 Camel routes 是可能的。

REST endpoint for a GET to /api/helo

就这么简单！我们刚刚为 URL */api/hello* 配置了一个 GET，用“Hello World！”。

如您所见，Apache Camel 是一个框架，它简化了与其他组件的集成，支持企业集成模式，并使创建数据管道变得更加容易。

希望你喜欢！谢谢大家！