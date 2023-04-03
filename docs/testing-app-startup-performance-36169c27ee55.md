# 测试应用启动性能

> 原文：<https://medium.com/androiddevelopers/testing-app-startup-performance-36169c27ee55?source=collection_archive---------2----------------------->

![](img/92a2a5d5cb2312146371d206ec4d74ff.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

## 测试发射性能可能很棘手，但也不一定如此

# 用于测试启动的 Shell 命令

我写这篇文章是为了解释更多关于性能、启动测试以及我用来测试启动的部分背后的原因。但是，如果你只是想要一些快速的东西，这就是:

1.  如果可能的话，锁定时钟(见下文)
2.  在命令行上运行(当您的设备连接时):

```
$ for i in `seq 1 100`
> do 
>   adb shell am force-stop com.android.samples.mytest
>   sleep 1
>   adb shell am start-activity -W -n com.android.samples.mytest/.MainActivity | grep "TotalTime" | cut -d ' ' -f 2
> done
```

上面的命令循环 100 次，启动一个应用程序，输出启动持续时间，并终止进程以准备再次执行所有操作。

# 测试启动性能…不明显

最近，我需要测试一个应用程序的启动性能(同时摆弄启动库，看看它是如何影响事情的——在以后的文章中会有更多的介绍)。我发现，就像我之前在[沿着这条路散步一样](http://graphics-geek.blogspot.com/)，测试启动性能……并不明显。

如果你正在测试一段运行时代码，有各种各样的方法去做它，从琐碎的“写一个紧密的循环并在`System.currentTimeMillis()`中计算增量”到更复杂和有用的东西，比如由 [AndroidX 基准](https://developer.android.com/jetpack/androidx/releases/benchmark)库提供的设施。

但是根据定义，应用程序启动的大部分过程发生在系统开始调用您的代码之前。那么你如何计算出到达那里需要多长时间呢？

我在 logcat 中四处闲逛，查看了一些底层 API，并询问了平台团队中的一些工程师，发现了一些有帮助的东西。更好的是，我能够使用`adb shell`工具中的工具来完全自动化我的测试和输出信息，这种方式使得将结果弹出到电子表格中并分析它们变得容易。

我将描述我在上面的命令中使用的部分，给你 1-2 个简单的步骤，如果*你*需要测试启动性能的话。

# ActivityTaskManager 启动日志

正如我之前在更早的(不幸的是，已经过时和不正确的)[博客](http://graphics-geek.blogspot.com/)中所写的，自从 KitKat 发布以来，系统已经发布了一个方便的日志。每当活动开始时，您都会在 logcat 输出中看到类似这样的内容:

```
ActivityTaskManager: Displayed com.android.samples.mytest/.MainActivity: +1s380ms
```

此持续时间(本例中为 1，380 毫秒)表示从启动应用程序到系统认为它“已启动”所花费的时间，包括绘制第一帧(因此“已显示”)。

这个`Displayed`持续时间并不一定包括你的应用在准备好之前需要做的所有事情。只要你的应用程序确定已经完全完成加载和初始化，你就可以通过调用`[Activity.reportFullyDrawn(](https://developer.android.com/reference/android/app/Activity#reportFullyDrawn()))`向系统提供额外的信息。如果/当您调用该可选方法时，系统会发出另一个具有该时间戳和持续时间的日志:

```
2020-11-18 15:44:02.171 1279-1336/system_process I/ActivityTaskManager: Fully drawn com.android.samples.mytest/.MainActivity: +2s384ms
```

我只想要显示的持续时间，所以内置日志对于我的目的来说已经足够好了。

# 自动化启动

性能测试应该总是包括多次运行测试，以减少结果中固有的可变性；你跑得越多，你对平均成绩就越有信心。我试着至少运行十次测试，但是做得更多会更好。根据您的情况下结果的可变性以及时间的长短(因为可变性对较短的持续时间有较大的影响)，可能需要进行更多的运行。

> 疯狂是一遍又一遍地做同样的事情，却期待不同的结果。
> ——阿尔伯特·爱因斯坦

性能测试推论:

> ***精神错乱*** *做同一件事只做一次，并期待结果是确定的。*
> 
> -不是阿尔伯特·爱因斯坦

连续多次点击应用程序图标来启动它是…相当乏味的。以可预测和一致的方式做到这一点(以避免引入可变性，就像你碰巧错误地启动了另一个应用程序，或者让系统做额外的工作来丢弃计时结果)可能是一个问题。

所以我真正想要的是某种从命令行启动应用程序的方式。然后，我可以运行那个命令来一遍又一遍地做同样的事情，避免手动启动应用程序的可变性(和乏味)。

`[adb](https://developer.android.com/studio/command-line/adb)` (Android Debug Bridge，一个读到这里的人可能都很熟悉的工具)提供了我需要的东西。更具体地说，`adb shell`提供了一个命令行界面来启动一个应用:`adb shell am start-activity`。该命令也应该阻塞，直到应用程序完成启动，所以我们也将使用`-W`参数(这对于下一步是必要的，在下一步中，我们将添加一个后续命令来在启动后终止应用程序)。以下是完整的启动命令:

```
$ adb shell am start-activity -W -n com.android.samples.mytest/.MainActivity
```

最后一个参数是应用程序的包+组件信息。您可以看到它们与上一节中的`ActivityTaskManager`日志输出相同。

运行此命令会启动应用程序(除非应用程序已经在前台，这不是您想要的；我们将在接下来处理它)，然后输出以下信息:

```
Starting: Intent { cmp=com.android.samples.mytest/.MainActivity }
Status: ok
LaunchState: COLD
Activity: com.android.samples.mytest/.MainActivity
TotalTime: 1380
WaitTime: 1381
Complete
```

查看`TotalTime`结果:这与我们在日志中看到的信息完全相同:

```
ActivityTaskManager: Displayed com.android.samples.mytest/.MainActivity: +1s380ms
```

这意味着我们不必通过 logcat 来获取这些信息。相反，我们可以从运行 launch 命令的控制台直接获得它。更好的是，我们可以去掉无关的文本，只留下启动结果，这样更容易提取这些数据供其他地方使用。

为了将上面的输出转换为启动持续时间，我通过`grep`和`cut` shell 命令传输输出(有多种方法可以做到这一点，我只是随机选择了一种):

```
adb shell am start-activity -W -n com.android.samples.mytest/.MainActivity | grep "TotalTime" | cut -d ' ' -f 2
```

现在，当我运行这个命令时，我得到了一个期望的数字:

```
$ [start-activity command as above...]
1380
```

# 创业是一道最好的冷盘

在检查启动性能时，最好理解“冷启动”和“热启动”之间的区别

*冷启动*发生在您的应用程序在安装、重启后第一次启动时，或者当它不在后台时。

*另一方面，热启动*是当应用程序已经启动并在后台运行(但已暂停)时得到的结果。

这两种场景都可以测试和理解。但是一般来说，在**冷启动**场景中测试你的启动性能是一个更好的开始，有两个主要原因:

*   **一致性**:冷启动确保你的 app 每次启动都在经历相同的一组操作。如果你的应用是在热启动场景下启动的，那么哪些步骤被执行，哪些步骤被跳过就不那么明显了，所以不清楚你实际上在计时什么(以及你是否在重复运行之间持续测试)。
*   **最坏情况**:根据定义，冷启动是最坏情况；这是您的用户将看到的最长的启动持续时间。您需要将注意力放在最差的统计数据上，而不是最好的热启动情况。如果你忽视这些大问题，你就无法解决它们。

为了在每次运行时强制冷启动，您需要在运行之间关闭应用程序。同样，在屏幕上这样做(比如说，从 launcher 的概览列表中划掉它)会很乏味而且容易出错。`adb shell`再次前来救援。

有几个不同的 shell 命令可以用来终止活动。最明显的是`adb shell am kill` …但它实际上并不奏效。在你启动你的应用程序后，它在前台，并且`kill`不会关闭前台应用程序。相反，你需要`force-quit`命令:

```
adb shell am force-stop com.android.samples.mytest
```

在这里你使用你的应用程序的包名来告诉它停止哪个应用程序。

# 我喜欢循环，循环

现在，您可以一起运行命令来启动应用程序，输出启动持续时间数据，并退出应用程序，使其准备好再次启动。你可以在控制台中一遍又一遍地输入这个，但是，嘿，我们在一个 shell 中；让我们把它放在一个循环中，只用一个命令重复运行它。

在这样做的时候，你要避免在应用程序被终止后过早地运行它，以防出现与应用程序被拆除相关的副作用(比如当应用程序被拆除时，系统将启动器拉到前台)。为此，我添加了第二个`sleep`来在操作之间插入一个小缓冲区。

这是我使用的最后一个命令，包括关闭应用程序，等待一秒钟，然后启动它。我循环了 100 次迭代，这为我的情况提供了一个合理的样本大小:

```
$ for i in `seq 1 100`
> do 
>   adb shell am force-stop com.android.samples.mytest
>   sleep 1
>   adb shell am start-activity -W -n com.android.samples.mytest/.MainActivity | grep "TotalTime" | cut -d ' ' -f 2
> done
```

当我运行这个程序时，每次启动完成时，控制台都会输出启动持续时间，这正是我想要跟踪和分析的。

注意:使用`-S`(首先强制停止活动)和`-R COUNT`(运行`start-activity`命令`COUNT`次)循环 start-activity 实际上有一种简单得多的方法，所以我可以用这个来代替:

```
$ adb shell am start-activity -S -W -R 100-n com.android.samples.mytest/.MainActivity | grep "TotalTime" | cut -d ' ' -f 2
```

但是为了确保在拆卸和启动之间有一个不活动的缓冲，我需要那个`sleep 1`命令，所以我使用了更冗长的循环方法。况且 shell 脚本代码 *sooooo* 优雅吧？

# 可能的话，锁定你的时钟

影响移动设备性能的因素之一是 CPU 架构，尤其是 CPU 频率。具体来说，移动设备保持电池寿命并避免过热的主要问题的主要方法之一是通过抑制 CPU 速度。

CPU 节流对电池寿命有好处，但对性能测试来说就不那么好了，因为在性能测试中，一致的结果是至关重要的。

理想情况下，在运行性能测试时，您应该能够控制 CPU 频率。不幸的是，您能否做到这一点取决于您所拥有的设备；您需要以 root 用户身份访问设备来控制 CPU 调控器，CPU 调控器控制频率，不同的设备可能有不同的方式来改变这种行为。

以下信息仅适用于您，前提是您碰巧可以访问允许它的设备，并且您可以获得 root 访问权限。在设备方面，我知道 Pixel 设备允许这种访问；其他设备我不敢说。

无论如何，*如果*你可以锁定你的时钟，我建议你这样做。对于您的特定测试情况，这可能并不重要(事实上，系统通常以高频率运行时钟，特别是在启动应用程序时，因此这可能已经提供了您需要的一致性)。但是至少消除 CPU 频率这个可变性因素是有好处的。

不幸的是，手动锁定 CPU 频率可能很棘手。幸运的是， [AndroidX 基准测试](https://developer.android.com/jetpack/androidx/releases/benchmark)库使它变得简单。事实上，您甚至不需要为基准 API 编写代码；您可以使用这个库来获得它提供的便利的`lockClocks`和`unlockClocks`实用程序。

首先，将基准库作为依赖项添加到项目级 build.gradle 文件中:

```
classpath "androidx.benchmark:benchmark-gradle-plugin:1.0.0"
```

接下来，通过将基准插件添加到您的应用程序级 build.gradle 文件来应用它:

```
apply plugin: androidx.benchmark
```

现在你可以同步你的项目了(Android Studio 可能已经在催促你这么做了)，之后锁定任务就可以从`gradlew`使用了。

现在，您可以通过在命令行上运行它来锁定您的时钟(我在 Android Studio 的终端工具中运行了它，但是您也可以在 ide 之外运行它):

```
$ ./gradlew lockClocks
```

当我这样做时，我在控制台中看到了以下输出:

```
Locked CPUs 4,5,6,7 to 1267200 / 2457600 KHz
Disabled CPUs 0,1,2,3
```

这个输出很好地表明它在我的 Pixel 2 上工作。一个更好的迹象是，我的启动测试现在比以前花费了更多的时间。但是等等，为什么锁定的时钟*比*慢？

基准测试工具将时钟锁定在一个容易维持的水平，而不是一个高性能的水平。如果时钟尽可能地高速运行，您可能会获得更好的性能，但是:

*   您希望测试结果具有真实的甚至很差的性能，就像您的许多用户在现场看到的那样。您不希望只看到最佳情况下的性能，这不是人们在现实中通常会看到的。
*   时钟以高频率运行太久会使它们过热。我不知道系统对此会有什么反应(希望它能在出现严重问题之前自动关闭系统)，但我真的不想知道。

请注意，当您完成测试后，您将需要*解锁*时钟。设备将在重新启动时解锁它们，但您也可以通过运行相反的梯度任务解锁它们:

```
$ ./gradlew unlockClocks
```

…只需重启设备即可执行重置。

(如果你想了解更多关于 benchmark 锁定工具的信息，请查看[用户指南](https://developer.android.com/studio/profile/benchmark#clock-stability))。

# 就这样…完成了！

在锁定时钟之后，我已经准备好了一切:一个可以可靠一致地再现我的启动情况的系统，以及一个我可以执行并返回结果流的简单命令行。我能够复制结果，将它们粘贴到电子表格中，并分析它们(通过将我的启动平均值与我想要试验的各种之前/之后的情况进行比较)。

理想情况下，我不需要一篇文章来解释如何做到这一切。老实说，你不需要上面所有的解释。(但是知道事物是如何工作的以及为什么工作总是更有趣，不是吗？)您真正需要的是单个`for()` loop shell 命令，以及可选的锁定时钟的方法。

1.  如果可能的话，锁定时钟(见下文)
2.  在命令行上运行(当您的设备连接时):

```
$ for i in `seq 1 100`
> do 
>   adb shell am force-stop com.android.samples.mytest
>   sleep 1
>   adb shell am start-activity -W -n com.android.samples.mytest/.MainActivity | grep "TotalTime" | cut -d ' ' -f 2
> done
```

为了更简单的性能测试和分析，以及更好的应用程序性能，该团队正在研究简化这一过程的方法。当我们有什么要分享的时候，请继续关注。但同时，我希望上面的命令和信息对您的启动测试需求有所帮助。