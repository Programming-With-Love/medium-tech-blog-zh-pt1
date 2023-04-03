# 建立稳定

> 原文：<https://medium.com/square-corner-blog/build-stability-82dabdf00c3a?source=collection_archive---------2----------------------->

## 我们如何停止重新运行失败的测试，并保持这种方式

*由* [撰写*迈克尔*](https://medium.com/u/22c9903aa9f1?source=post_page-----82dabdf00c3a--------------------------------) *。*

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

实践持续集成(CI)的公司中的每个开发人员都会在某个时候就他们的构建进行以下对话:

开发人员 1:“我的构建失败了，但是我没有更改任何与*testSwissCheeseController*”
相关的内容。那个测试对每个人都失败了。这不是你的错”

这是一种令人沮丧的交流，尤其是当它随机发生并阻碍重要工作时。只有当来自代码的信号可信时，代码的自动检查才有用。不幸的是，这些假想的开发人员描述的那种失败很难重现、诊断、修复和验证，原因与令人愤怒的原因完全一样:它不是一直都在发生。

您可以使用许多最佳实践来减少测试中的共享状态，编写更好的测试，并主动防止这类失败。但是当这些技术失败时，你会怎么做？

# 测量误差

CI 构建试图衡量应用程序代码是否是好代码。好的代码构建并通过测试。我们无法衡量代码是不是好代码；我们只能测量代码是否在特定的一天在特定的机器上通过一次测试。

我们有两种主要的度量错误:假阴性，好代码的构建是红色的，假阳性，坏代码的构建是绿色的。通常，开发人员和构建系统将绿色构建视为“安全合并”的信号。因此，低概率的假阴性可能会在母版上累积，并且永远不会得到修复。假阴性也可以通过对至少有一个绿色版本的代码运行测试来衡量。

假阳性不容易测量。考虑一个无论应用程序代码如何都始终通过的测试。很难自动找到这种测试。在 Square，我们在构建脚本中使用主动技术来避免 CI 系统本身的假阳性错误。最值得注意的是，我们在 [ruby](https://github.com/square/build_execution) 和 [bash](http://redsymbol.net/articles/unofficial-bash-strict-mode/) 中使用了执行结构，确保任何异常退出的进程都是直接的构建失败，除非被显式处理。

每天晚上，我们在 CI 集群上重新运行前一天的成功构建，以测量误报。并行使用我们所有的构建机器，我们可以在大多数开发人员不工作的时候完成几百个构建。每天早上，我们清点失败的案例并进行分类。这是一个类似于面向消费者的应用程序崩溃分类的任务。一些失败被归档为 bug，并根据它们的性质和大小分配给适当的团队。

# 测量误差

当我们从一个晚上的构建中获取数据时，我们只有构建在特定修订中运行的次数和失败的次数。这个失败率本身有时会误导对错误本质的理解。

构建团队有责任维护 99%可靠性 SLA。我们已经注意到，当一个 build 有<1% chance of failing, developer trust in the builds increases. Failures above the 1% level are very visible and frustrating — even in a group of a couple dozen developers.

Sometimes a 1% error won’t recur in 200 runs. Sometimes a 0.1% error will happen twice in the same number of builds. We have limited build machine resources and limited people available for triage and investigation. How do we use the failure percentage in order to allocate those resources well? We can set this up as a statistics problem.

Given the total number of builds and failures, we’re looking for a way to guess the range of the actual probability of failure. The [二项式分布](http://en.wikipedia.org/wiki/Binomial_distribution)描述了一个不公平硬币的行为，其正面概率为 P。稳定性检查构建是一个非常不公平的硬币，因为它们通常有非常接近 1 的成功概率。

我们使用[威尔森得分区间](http://en.wikipedia.org/wiki/Binomial_proportion_confidence_interval#Wilson_score_interval)来计算我们测量的概率的置信区间。威尔逊得分区间是基于正态分布的[二项式置信区间](http://en.wikipedia.org/wiki/Binomial_proportion_confidence_interval#Clopper-Pearson_interval)的极值近似值。

思考这个问题的一个好方法是考虑一个失败概率为 1%的构建。我们运行了 200 次，只有一次失败。因此，我们可以得出结论，失败率为 0.5%。这是一个很好的猜测，但我们只有一个数据点。如果它在第 201 次试验中再次失败，我们的猜测将发生巨大变化。同样，如果我们在前 10 次构建中看到同样的失败，我们会猜测它发生的几率是 10%。这是一个合理的猜测，但是如果再进行 10 次没有其他失败的试验，我们的预期将会有很大的改变。

在上面的第一个例子中，威尔逊计算的 80%置信区间是 0.27%-1.54%。在第二个示例中，它是 5.5%-26.4%。这些范围显示了我们在有限的试验次数下的实际发现，优于单独的 0.5%或 10%的次数。这些范围量化了我们目前对故障及其严重程度的理解。

这个电子表格提供了概率模型的可视化表示。第一页让您输入构建和失败的数量。第二个显示了二项式分布，以及 90%、80%和 50%的置信区间。电子表格进行了数值积分，因此结果不准确，与 Wilson 分数计算不匹配。

# 履行

在 Square，我们使用置信区间的宽度来决定是否提交 bug。如果置信区间宽度小于该类别的观察误差百分比，我们就提交一个 bug。如果我们观察到一个更大的错误百分比，我们就把错误的优先级设置得更高。我们已经在内部校准了此流程的阈值，以确保影响总体 SLA 的错误得到适当的优先处理。

这种技术非常适合于引起人们对构建和测试系统的错误的注意，因为这些错误发生的频率太低了。对于经常发生的事情或众所周知的故障模式，积极主动的方法更有效。

基于置信区间做出决策有助于 Square 从 iOS CI 系统中挤出最后一点错误。我们找到了配置构建奴隶的方法来避免 Xcode 和模拟器的错误。我们发现并修复了一个影响数百次测试的罕见内存错误处理错误。在将 Register 部署到我们的手动测试器之前，我们还能够在新特性中找到几个竞争条件。

在我们最稳定的时候，我们在一周内测量了 1500 个构建中的一个失败。接近 99.9%的稳定性大大增加了对 iOS CI 系统的信任。我们正在努力将这种方法和分析应用到 Square 实施 CI 的其他领域。

*本帖是 Square 的“* [*周 iOS*](https://corner.squareup.com/2015/06/a-week-of-ios.html) *”系列的一部分。*

[](/@mtauraso) [## 迈克尔·陶拉索-简介

### 请关注迈克尔·陶拉索的最新活动-在媒体上发布个人资料。96 人正在关注迈克尔·陶拉索的个人资料…

medium.com](/@mtauraso)