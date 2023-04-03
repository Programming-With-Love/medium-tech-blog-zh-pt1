# 后台任务检查器

> 原文：<https://medium.com/androiddevelopers/background-task-inspector-30c8706f0380?source=collection_archive---------5----------------------->

![](img/2f304fe68e00fc7d38aad6625499a4bb.png)

Android Studio 包括多个检查器，如[布局检查器](/androiddevelopers/layout-inspector-1f8d446d048)和[数据库检查器](/androiddevelopers/database-inspector-9e91aa265316)，帮助您调查和了解您正在运行的应用程序的内部状态。通过 Android Studio 北极狐，我们将发布一个新的检查器，帮助您使用 WorkManager 2.5.0 或更高版本监控和调试您的应用程序调度。

[工作管理器](https://developer.android.com/topic/libraries/architecture/workmanager)是在后台运行异步任务的推荐方式，即使在应用关闭之后。虽然将任务配置为工作管理器工作器很容易，但是一旦工作器被排队，它的执行就很难监控，如果遇到问题，调试起来也很棘手。

使用后台任务检查器，您可以轻松地监视工作线程的状态，查看它与其他链接工作线程的关系，或者检查详细信息，如工作线程输出、频率和其他计时信息。让我们看看后台任务检查器可以对一个示例项目做些什么。

为了演示后台任务检查器，我将在[架构组件报告](https://github.com/android/architecture-components-samples/tree/main/WorkManagerSample)中使用 WorkManager 示例应用程序。如果你想试一试，你可以查看回购协议，并在阅读文章时跟进。

这个应用程序使用工作管理器在选定的照片上应用用户选择的过滤器。运行该应用程序后，用户可以从图库中选择一张照片，或者简单地使用随机的库存照片。为了查看后台任务检查器是如何工作的，我运行了这个应用程序，并选择了一个图像来应用一些滤镜。

![](img/f388e031a05091df636c610b90618d45.png)

The app applies the selected filters on the selected image

每个过滤器都实现为一个工作管理器工作器。短暂等待后，应用程序会显示应用了所选滤镜的照片。在没有任何关于示例应用程序的进一步知识的情况下，让我们来看看通过使用后台任务检查器我还能发现什么。

要打开后台任务检查器，请从菜单栏中选择“视图”>“工具窗口”>“应用程序检查”。

![](img/f4e22ca310fdd1806e1cf3fcfdf1dd4b.png)

View > Tool Windows > App Inspection

在应用检查窗口中选择后台任务检查器选项卡后，我在运行 API 级别 26 或更高的设备或仿真器上再次运行该应用。如果不是自动选择，我从下拉菜单中选择 app 流程。连接到应用程序进程后，我可以转到正在运行的应用程序，选择所有过滤器，然后单击应用。这次我可以在后台任务检查器中看到正在运行的作业列表。

![](img/2c59bea3556cf117cdf84ea4df45780b.png)

List of running jobs

后台任务检查器列出所有正在运行/失败或已完成的作业的类名、当前状态、开始时间、重试次数和输出数据。单击列表中的特定作业将打开工作详细信息面板。

![](img/9b253a40380b252fd99f1ac8e1c3bc31.png)

Details Panel

该面板为每个工人的描述、执行、工作继续和结果提供了更详细的信息。让我们仔细看看每一部分。

![](img/9e5c8768af62569715c214bd34c4199c.png)

Work Details

Description 部分列出了带有完全限定包的 worker 类名，以及该 Worker 的分配标记和 UUID。

![](img/aee86be0de9d82f7a1092d3cbcff43c9.png)

Execution

接下来，execution 部分显示了 worker 的约束(如果有的话)、运行频率、它的状态以及哪个类创建了这个 worker 并对其进行了排队。

![](img/b98252ee0e4ab23099118d3a8f4b7a17.png)

WorkContinuation

工作连续性部分显示该员工在工作链中的位置。您可以检查上一个或下一个员工(如果有)，或者工作链中的其他职务。您可以单击另一个员工 UUID 来导航并查看所选员工的详细信息。查看这个工作链，我可以看到该应用程序使用 5 个不同的工人。根据用户选择的过滤器，工人的数量可能会有所不同。

这很好，但如果你不熟悉这个应用程序，想象工作链并不总是很容易。后台任务管理器检查器的另一个重要特性是能够以图形的形式显示工作链。为此，我单击工作连续性部分中的“在图表中显示”链接，或者通过单击作业列表顶部的“显示图表视图”按钮切换到图表视图。

![](img/6dd792766245acc831690da94efed653.png)

Graph View

这个图形视图可以帮助您了解工作器的顺序、工作器之间传递的数据以及每个工作器的当前状态。

![](img/d8118494a8864c5af8ef8a8e1360c500.png)

Results

工作细节面板中的最后一部分是结果窗格。在这一部分中，您可以看到启动时间、重试次数和所选工人的输出数据。

假设我想测试当一个工人停止时会发生什么。为此，我将再次运行应用程序，选择工作人员，等到它变为运行状态，然后单击左上方的“取消所选工作”。一旦我这样做了，我选择的工人和链中的其余工人将他们的状态更改为“已取消”。

![](img/58a997332d19f108fd2831a088d52588.png)

You can cancel any running worker

如果您的应用程序包含复杂的链接关系，比如这里的链接关系，图表视图会变得更加有用。该图表允许您快速查看一组复杂员工之间的关系，并监控他们的进度。

![](img/b17ff1d41ac509a8aa11cfb13242d43e.png)

Here is some WorkManager art =)

如果你想用一个更复杂的图形来尝试后台任务检查器，或者制作一些工作管理器艺术，你可以在这里找到代码[并将 DummyWorkers 添加到延续对象中，如图所示](https://gist.github.com/yenerm/60207091db3b9c1b0eca9b70f6475cd7)[这里](https://gist.github.com/yenerm/b9744b24614fb29cedf47e36df3248b4)。

后台任务检查器将在 Android Studio 北极狐版本中提供，但它已经可供您在北极狐预览频道中尝试，并且很快将处于测试阶段！如果您的应用程序使用工作管理器，请尝试一下，让我们知道您的想法，或者简单地与我们分享您的工作管理器艺术！