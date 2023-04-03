# 克服谷歌应用引擎 Cron 的缺陷

> 原文：<https://medium.com/google-developer-experts/pitfalls-of-using-cron-jobs-with-google-app-engine-bf005f8bf7e6?source=collection_archive---------0----------------------->

![](img/af87c659bace17eef9e34b5cfd7fb0f0.png)

在 https://roobits.com[的数据管道中大量使用了 GCP 之后，我们一直在寻找一种有效的方式来调度特定的任务定期运行](https://roobits.com)

例如，在之前的博客中，您可能还记得我们允许企业以每秒的速度从他们的网站/应用程序中收集事件，并将它们存储到 BigQuery 中，以便以后进行检索/分析。

[](/google-developer-experts/trimming-down-over-95-of-your-bigquery-costs-using-file-loads-d08dd3d8b2fd) [## 使用文件加载降低 95%以上的大查询成本

### TL；dr:使用文件加载

medium.com](/google-developer-experts/trimming-down-over-95-of-your-bigquery-costs-using-file-loads-d08dd3d8b2fd) 

虽然我们的主要目标是实时完成这项工作，但我们的一些客户要求以更低的成本进行批量选择。

为了实现这一点，我们可以将传入的请求保存到 App Engine 实例中的一个文本文件中，并运行一个 cron 作业将该文件加载到 BigQuery 中(**记住，将文件加载到 BigQuery 中是免费的！每隔几分钟。**

令人欣慰的是，App Engine 有一个内置的 cron 服务，允许您编写一个简单的`**cron.yaml**`，其中包含您希望作业运行的时间以及它应该到达的端点，就是这样！

App Engine 将确保 cron 在您指定的时间执行。

这里有一个示例`**cron.yaml**`，它每 2 分钟到达我们 AppEngine 部署中的`**/upload**`端点。

```
**cron:
description: "Bigquery Load Job"
url: /upload
schedule: every 2 mins**
```

# 然而…

虽然理论上听起来不错，但 App Engine 提供的解决方案在我们的用例中却失败了。
为什么，你可能会问！

原因很简单，cron 作业将只在您的应用引擎部署的一个**实例**上运行！

这对我们来说是一个交易破坏者，因为我们有超过 12 个应用引擎实例在运行，每个实例都有一个包含过去 2 分钟用户数据的文本文件。

由于 cron 作业只针对一个实例，**那么我们基本上错过了总数据的 90%以上**，这是完全不可接受的！

不仅如此，使用 cron job，您还**无法将您部署的服务的特定版本作为目标**,这使得我们无法进行 A/B 测试。

Cron job 还受限于这样一个事实，即它只能在您在`**cron.yaml**`中指定的端点发出`**/get**`请求，从而使您的服务器容易受到爬虫和其他攻击者的攻击。

> ***编辑:*** *您可以通过设置 AppEngine 中的* `*X-Appengine-Cron*` *头来验证您的 cron 请求。请访问此页面了解更多信息:*

[](https://cloud.google.com/appengine/docs/flexible/nodejs/scheduling-jobs-with-cron-yaml#validating_cron_requests) [## 使用 cron.yaml 调度作业

### App Engine Cron 服务允许您配置在定义的时间或定期运行的定期计划任务…

cloud.google.com](https://cloud.google.com/appengine/docs/flexible/nodejs/scheduling-jobs-with-cron-yaml#validating_cron_requests) 

# 可供选择的事物

由于使用 cron 作业是不可能的，我们开始寻找替代方案，第一个是**云调度器**。

而 Cloud Scheduler 拥有我们想要的一切，包括针对特定版本、实例、服务的选项，还向处理 cron 功能背后的逻辑运行的端点发送 POST 请求。

虽然 AppEngine 提供的 cron 是免费的，但是 **Cloud Scheduler 每月每个任务收取 0.1 美元**。

假设我们必须每 2 分钟运行一个作业，如果我们使用云调度程序，我们必须承担的总成本是 **0.1$** 。

同样需要注意的是，使用 Cloud Scheduler 创建的前 3 个任务是免费的，所以如果你有一个应该每月运行多次的脚本，你不需要支付任何费用！

*感谢*[*vine et garg*](https://medium.com/u/1047e5240ceb?source=post_page-----bf005f8bf7e6--------------------------------)*指出云调度器是按作业定价的，而不是按执行定价的！*

我们偶然发现的第二个解决方案是使用我们自己的定制 cron，它运行在我们的服务器上。

虽然它需要我们编写一个基本的脚本来跟踪已经过去的时间，但它是免费的，没有 App Engine 的 cron 所具有的任何限制。

我们最终在部署在 App Engine 上的服务器中编写了以下代码片段。

```
**let job = new CronJob('2 * * * * *', function() {
    uploadFile(function(err){
        if(err) console.log(err);
        else return "Valid Cron";
    });
});****job.start();**
```

我们创建的作业每两分钟运行一次，并调用方法`**uploadFile**` 将文件加载到 BigQuery 中。

这也可以在每个实例上运行，因为它不会影响公开的 API，所以它消除了我们之前遇到的关于解决方案的实用性和安全性的所有问题。

# 结论

如果你是一个和我们处境相似的人，我认为花时间编写你的定制 cron 作业是值得的，而不是依赖于预先构建的服务。

虽然它需要我们编写一些代码，但正如您所看到的，它只有几行代码，我们从中获得的好处值得我们投入 15 分钟的时间。

*感谢阅读！如果你喜欢这个故事，请点击**👏 ***按钮*** ***并分享*** *帮助别人找到！欢迎留言评论*💬*下图。**

**有反馈？下面我们来连线推特上的*[](https://twitter.com/harshithdwivedi)**。***