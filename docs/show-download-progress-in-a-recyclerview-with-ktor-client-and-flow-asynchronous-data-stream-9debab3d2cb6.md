# 使用 Ktor 客户端和流异步数据流在 RecyclerView 中显示下载进度

> 原文：<https://blog.kotlin-academy.com/show-download-progress-in-a-recyclerview-with-ktor-client-and-flow-asynchronous-data-stream-9debab3d2cb6?source=collection_archive---------2----------------------->

![](img/77862957eabca8c2603741d8c6bdb07c.png)

photo by [Paul Downey](https://www.flickr.com/photos/psd/)

[在第一个关于用 Ktor 和 Koin](/download-files-with-ktor-and-coroutines-e96b1cc8b657) 下载文件的故事里(真的建议先看完再开始看这个故事！)，我们学习了如何从回收器的角度下载一些文件，但是我很确定你们中的很多人都想知道***嘿，但是显示一个确定的进度条怎么样？*”。**事实上，为了更好的用户体验，让用户知道等待文件下载**的时间是很重要的。因此，在这个故事中，我们将看到协程如何再次派上用场，拯救我们的开发人员的生命！**

首先要考虑的是:" ***我如何用 Ktor-client 获取下载文件的进度？"。*** 干脆就这样 ***:***

我们没有使用任何魔法，只是将我们的`response`设置在一个变量中，创建一个`data` ByteArray 来保存文件的每个**块并读取它。在 while 循环中，我们有每个读取块的**进度**。现在我们知道了如何获得下载进度，我们应该考虑如何将它发送给调用者…这是一个密封类的完美场景！**

> 当值可以具有有限集合中的一种类型，但不能具有任何其他类型时，密封类用于表示受限制的类层次结构

调用者方法需要知道当前下载的**进度、****成功**和**失败**。特别是，失败可以传输一个消息直接展现给用户。

密封类不足以向列表适配器发送实时进度，对于我们的目的来说， **Kotlin** [**流**](https://kotlin.github.io/kotlinx.coroutines/kotlinx-coroutines-core/kotlinx.coroutines.flow/-flow/) 是完美的！

> 流是*一个冷异步数据流，顺序发出值，正常或异常完成。*

***我们可以把它看作是一个 emitter 的采集器，采集器接收每一个信号(emit)并对每一个信号做一些运算*** 。这是一个非常简单的解释，但你可以在[kot Lin 流的简单设计](https://medium.com/@elizarov/simple-design-of-kotlin-flow-4725e7398c4c)中找到一个非常好的深度研究！
现在我们可以将`downloadFile`方法修改为**发射** `DownloadResult` …

…并且**在主活动中收集**结果:

不同情况下该怎么办？

*   *成功:*将下载状态设置为假
*   *错误:*显示信息并设置错误状态
*   进度:显示进度并更新进度条

不要忘记使用`withContext(Dispatchers.Main)`在主线程上完成所有这些操作

现在让我们看看适配器是如何变化的:

从上面的代码中，你可以看到使用`onBindViewHolder`和`payload`来为适配器中的每个视图选择准确的更新，这避免了每个`setProgress`在每个项目上令人讨厌的闪烁

所以这是最后的结果:

![](img/8edbb1c76b433ddc5257fd1d597c4242.png)

您可以在这里找到示例项目。编码快乐！

 [## 弗朗切斯科加托/Ktor-Koin-文件-下载

### 此时您不能执行该操作。您已使用另一个标签页或窗口登录。您已在另一个选项卡中注销，或者…

github.com](https://github.com/francescogatto/Ktor-Koin-Files-Download) 

# 单击👏说“谢谢！”并帮助他人找到这篇文章。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察 Twitter](https://twitter.com/ktdotacademy) 并在媒体上关注我们。

如果您需要 Kotlin 工作室，请查看我们如何帮助您: [kt.academy](https://www.kt.academy/) 。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)