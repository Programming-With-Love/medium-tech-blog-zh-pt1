# 使用 Google Cloud 数据流对流式输入源进行微批处理

> 原文：<https://medium.com/google-developer-experts/micro-batching-a-streaming-input-source-using-google-cloud-dataflow-ccd30d2aabf2?source=collection_archive---------3----------------------->

![](img/af87c659bace17eef9e34b5cfd7fb0f0.png)

通常，在构建数据处理管道时，您的数据可以是有界的(大小已定义)或无界的(大小未定义)。

一个**有界的**数据源的例子可以是包含牛津词典中所有单词的文本文件，而包含特定#hashtag 的所有 Tweets 的 twitter 流是一个**无界的**数据源的例子。

Google Cloud Dataflow 允许您处理这两种类型的数据源，为您提供了创建流或批处理管道的选项，分别处理无界和有界数据源。

在我们的用例中，我们必须处理来自无界数据源的数据，但是我们不想立即处理这些事件；相反，我们希望每两分钟对收集的事件进行一次操作。

我们尝试使用批处理管道来完成这项工作，但是在批处理模式下，数据流无法读取未绑定的源。这是由设计决定的，因为批处理管道期望读取有限数量的数据，并在处理完数据后终止。

当我们切换到流管道时，数据流希望我们一进入管道就对事件进行操作；这是我们不想做的事情。

> 感谢阅读这篇博客，如果你在一家高增长的公司工作，并且正在寻找一个大规模的实时数据收集平台；看看 https://roobits.com/的[T5。
> 我们可能就是您要找的人！](https://roobits.com/)

# 我们是如何解决这个问题的？

输入[分片](https://beam.apache.org/releases/javadoc/2.0.0/org/apache/beam/sdk/io/WriteFiles.html#withNumShards-int-)！
Dataflow 允许开发人员将所有进入管道的输入事件写入一个文件，以便在以后的某个时间点进行处理。

数据流中出现的几乎每个 IO 转换都支持分片；例如，我已经将它与 BigQueryIO 和 TextIO 一起使用，但它也可以与 KafkaIO、AvroIO 等一起使用。

分割传入事件需要两件事:

1.  **要创建的碎片数量**
2.  **触发频率**

碎片的数量实质上是数据流应该创建的用于写入传入事件的文件的数量。
因此，如果您将碎片数量设置为 10，dataflow 将创建 10 个文件，并在这 10 个文件中写入您的传入事件。
拥有大量碎片可以确保如果其中一个文件被破坏，其他未被破坏的文件中的其余事件仍然安全，从而降低单点故障的风险。

触发频率本质上就是你要读取上述文件的时间之后。它可以是你想要的任何东西，但是请记住，更长的时间意味着创建更大的文件！

**就这样！**

对于我们的用例，我们希望每 2 分钟处理一次事件并将其加载到 BigQuery 中，因此我们将数据流管道修改为:

```
.apply("Write to Custom BigQuery",
	BigQueryIO.writeTableRows()
	**.withNumFileShards(30)
	.withTriggeringFrequency(Duration.standardSeconds(90))** 	.withMethod(BigQueryIO.Write.Method.FILE_LOADS)
	.withSchema(tableSchema)
	.to(table);
```

出发地:

```
.apply("Write to Custom BigQuery",
	BigQueryIO.writeTableRows()
	.withSchema(tableSchema)
	.to(table);
```

只需要两行代码就可以将我们的流式管道转换成在指定时间对传入事件进行微批处理的流式管道。

> **注意**:默认情况下，数据流将文件存储在谷歌云存储桶中，因此使用这个可能会产生 GCS 收取的存储成本。

*感谢阅读！如果你喜欢这个故事，请* ***点击*** 👏 ***按钮*** ***并分享*** *帮助别人找到它！欢迎发表评论*💬*下图。*

*有反馈吗？下面我们来连线推特上的*[](https://twitter.com/harshithdwivedi)**。**