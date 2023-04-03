# 使用 Apache Camel 和 AWS S3 处理大型文件

> 原文：<https://medium.com/globant/large-file-processing-using-apache-camel-with-aws-s3-37c6633082cd?source=collection_archive---------0----------------------->

![](img/23ce1a82e4b393e052f2751f1535d78b.png)

# **简介**

Apache Camel 是一个开源集成框架，使您能够快速轻松地集成各种使用或产生数据的系统。Camel 支持 Gregor Hohpe 和 Bobby Woolf 的优秀著作中的大多数企业集成模式，以及来自微服务架构的更新的集成模式，通过应用开箱即用的最佳实践来帮助您解决集成问题。

更多详细信息请访问— [骆驼遗址](https://camel.apache.org/)

在早先的一篇文章中，我的同事[拉克什·拉夫勒卡](https://medium.com/u/c7b424f3e32?source=post_page-----37c6633082cd--------------------------------)解释了我们在这里[遵循的方法](/globant/file-processing-with-apache-camel-aws-services-6c18ee6c49f)

在这篇文章中，我将分享大文件处理用例的细节。

## 用例

*我们的要求是通过 Camel 从 AWS S3 桶中读取一个大文件****(>1.5 GB)****，对其进行处理，然后将生成的输出文件****(>1.5 GB)****上传到目的地 S3 桶中。*

# 通过实例学习

用一个具体的例子学习总是比较容易的！这就是为什么在这篇文章中，我将向你展示如何为这个场景创建一条骆驼路线。

1.  **从 S3 读取文件** —要从 S3 读取文件，我们有以下两种选择:

*   ***读取整个文件*** —该操作仅适用于文件**小于 25 MB** 的情况。

> Ex — aws2-s3://test-bucket？S3 client = # client & repeat count = 1 & deleteAfterRead = false & fileName = testfile . dat

*   ***分块读取文件*** —在这种方法中，您需要设置一个额外的骆驼 S3 选项，名为 **getObjectRange** 。此选项用于下载对象的指定范围字节。在这篇文章中，我们将使用这个选项来读取大文件数据，关于 HTTP 范围头的更多信息，请参见和

> Ex — aws2-s3://test-bucket？S3 client = # client & repeat count = 1 & deleteAfterRead = false & fileName = testfile . dat &**operation = getObjectRange**

2.**将文件上传到 S3 存储桶—** 要将文件上传到 S3，我们有以下两个选项:

*   ***上传整个文件*** —该选项仅适用于**小于 25 MB** 的情况。

> Ex — aws2-s3://test-bucket？s3Client=#client

*   ***多部分上传*** —为了分部分上传文件，我们需要设置另外两个选项，称为**多部分上传和部分大小**。此选项用于上传大于 25 MB 的文件**，但是您可以更改部分大小，因为我已更改为 10 MB，这意味着一个上传部分的大小为 10MB。**

> **Ex — aws2-s3://test-bucket？S3 client = # client &**multipart upload = true&partSize = 10485760****

**更多关于骆驼 AWS S3 组件的内幕会在这里找到[](https://camel.apache.org/components/3.9.x/aws2-s3-component.html)*。***

***为了分块读取文件，我们必须有文件长度来转换成范围，所以下一个问题是你如何知道文件长度？接下来是 **AWS S3 SDK** ，我们需要对 S3 做额外的 **HeadObjectRequest** 如下-***

> ***head object request head object request = head object request . builder()。桶(“测试桶”)。key("testfile.dat ")。build()；
> head object response head response = S3 client . head object(head object request)；
> int file length = head response . content length()。int value()；***

*****注意**—“*要处理 S3 操作我们必须有****S3 client****并且你可以借助****ApacheHttpClient****库*”。***

***有了文件长度后，我们需要按照自己的逻辑定义范围。
例如，假设文件大小为 **8192000** 字节，那么范围数组列表包含 4 个对象- **(0，2047999)(2048000–4095999)..直到字节的结尾**，我们将使用 camel 的 **split body** 函数迭代这个范围列表。***

***好了，到目前为止，我们已经完成了基本步骤，现在让我们为这个用例创建路线。***

# ***骆驼路线定义***

> ***从(直接(“开始”)
> 。noStreamCaching()
> 。
> 。**处理**(后置处理器)
> 。choice()
> 。when(header(AWS2S3Constants。内容 _ 长度)。isGreaterThan(MULTIPART _ LIMIT))
> 。to(" **aws2-s3://test-bucket？S3 client = # client&multipart upload = true&partSize = 10485760**"
> 。否则()
> 。to(" **aws2-s3://test-bucket？S3 client = # client**"
> 。end()
> 。end()
> 。**拆分(body())**
> 。
> 串流()。process(exchange->{
> **item dto item =(item dto)exchange . getin()。getBody()；
> exchange.getIn()。setHeader(AWS2S3Constants。RANGE_START，item . get from())；
> exchange.getIn()。setHeader(AWS2S3Constants。RANGE_END，item . getto())；
> exchange.getIn()。setHeader(AWS2S3Constants。KEY，test file . dat)；**
> })
> 。to(" **aws2-s3://test-bucket？S3 client = # client&repeat count = 1&deleteAfterRead = false&fileName = testfile . dat&operation = getObjectRange**"
> 。
> 进程(FileProcessor)。
> 元帅(bindy)。到(**文件(tempFilePath)。fileExist("Append ")。文件名(临时文件名)** )
> 。end()；***

*****路线说明*****

1.  ***执行从名为 start 的直接端点开始，在这个直接端点上，我们将传递我们创建的范围数组列表。***

***要启动路线，你有两个选择-***

*   ****使用监制模板-****

> ****producer template pt = context . createproducer template()；
> pt . send body(**" start "**，createRangeData())；****
> 
> ****createRangeData 方法将返回**列表<项到>******

*   *******从任何路线调用***—****

> ****from(timer("startTimer ")。重复计数(1))。noStreamCaching()
> 。过程(e - > e.getIn()。set body(createRangeData())
> 。到(**直接(“开始”)**)。end()；****

****2.为了迭代范围列表，我们使用了**分割体函数**来进一步获取和处理范围对象。****

****3.我添加了一个处理器，它将添加一些必要的 S3 头，如关键，开始范围和结束范围。****

****4.一旦标题准备好，我们需要从 S3 获取特定的范围数据。为此，我使用了一个函数来请求 range 对象。****

****5.收到 range 对象后，我们需要处理它。处理逻辑取决于您的业务场景。 **getObjectRange** 操作将在 camel 交换中返回一个字节流，因此，要在**处理器**中获得该流，您需要添加以下代码-****

> ****ResponseInputStream RES =(ResponseInputStream)exchange . getin()。getBody()；
> byte[]message bytes = RES . read all bytes()；这些消息字节将根据您的逻辑进一步使用。****
> 
> ****Camel 还提供了一个使用并行流并行处理数据的工具，如-
> **——为此您需要添加。route 中的 parallelProcessing()函数，并且您还可以覆盖 camel 上下文中的默认 ThreadPoolProfile，如 context . getexecutorservicemanager()。setDefaultThreadPoolProfile(MyThreadPoolProfile)；
> **使用 Executor 或任何其他框架**——如果您想使用 Executor Service 或 Akka 等多线程框架并行处理数据，请在处理器内部使用。******
> 
> ****点击 [**此处**](https://camel.apache.org/components/3.9.x/eips/split-eip.html) 了解 camel 中并行处理的更多详情。****

****6.一旦您的处理完成，我们需要使用 camel bindy 整理数据以生成 CSV 文件，但您可以根据您的业务需求将其整理成固定长度的文件等。****

****7.然后我们将数据发送到 [**文件组件**](https://camel.apache.org/components/3.9.x/file-component.html) 。****

****注意—“*这只是我们存储到本地文件系统的已处理数据的一个范围，或者您可以说我们正在逐个收集数据，一旦收集了所有范围的数据，我们就会将该文件上传到 s3* ”。****

****8.为了启动上传，我们添加了 onCompletion hook 并添加了**后处理器**，它运行用于数据的最终处理，因此在其中我们只将文件对象设置为交换体。****

> ****File File = new File(tempfile path+"/"+TEMP _ File _ NAME)；
> message.setBody(文件)；****

****9.最后，我们将使用 **multiPartUpload** 选项向 s3 发出上传请求。****

# ******结论******

****这就是我们如何用 Camel 和 AWS S3 来处理大文件。在我们当前的 AWS 配置( **CPU — 1024 单元& RAM — 2048 Mib** )上，我们能够在不到 15 分钟的时间内处理几乎 **1.5 Gb** 的文件。****

****希望你喜欢这篇报道。如果你有任何问题/意见，请给我留言！****