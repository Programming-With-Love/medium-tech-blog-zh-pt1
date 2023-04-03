# 澄清 React-Native 中一些最模糊的崩溃报告

> 原文：<https://medium.com/quick-code/clarifying-some-of-the-most-ambiguous-crash-reports-in-react-native-25ba6bbe48f9?source=collection_archive---------0----------------------->

![](img/7762ecad2b126c2aa87d4124aec303c6.png)

Reference [https://facebook.github.io/react-native/blog/2018/11/01/oss-roadmap](https://facebook.github.io/react-native/blog/2018/11/01/oss-roadmap)

本文整理了 React-Native 中一些最不明确的崩溃报告的清单，解释了它们的原因并提出了可能的解决方案。然而，判断框架的任何方面或评估某些组件的过程完全超出了这里的范围。

我选择写这篇文章的原因是因为我在使用 React-Native 开发最近发布的平板电脑应用程序时遇到的挫折。我在 Fuego 的日常职责包括从网站开发、前端原型开发到移动应用开发。然而，后者并不经常出现在我的盘子里，因为开发团队配备了 iOS 和 Android 的本地开发资源。尽管如此，我们还是选择了混合方式来应对时间紧迫的问题，并在两个平台上发布应用程序。

React-Native 是一个流行的框架，并且发展迅速，这一事实让我倍感压力。我上一次使用 RN 进行 app 开发是在两年前。那时，RN 是 0.43，从那以后发生了很多变化，显示了错误和错误，本地模块 API，停止支持一些第三方模块，等等..

幸运的是，在最新的版本中，我个人对商店管理和线下优先思想的体验非常好。变得糟糕的是我不得不处理事故报告的时候，这些报告并没有说太多的情况。

## 我记录的观察尤其是在 Android 上，并不是说 Xcode 完全顺利，但错误至少是明显的，直截了当的。

当您最终调试不可再现的零星错误时，这是令人不快的。对于一个扩展到大约 25 个屏幕并连接大约 40 个 web 服务的应用程序来说，编译一个在启动前崩溃而没有留下任何标记或日志的版本不是一个好兆头。由于我在解决这些问题时遇到了阻碍，这促使我想与社区分享我的发现。

下面列出的崩溃报告是基于它们的模糊程度。

## **1-不是对象的值的请求键**

![](img/c78bbe93090e36d80d3e5d2887bedbb8.png)

Emulator screenshot showing the crash report

这是有史以来最糟糕的，但却是微不足道的，也是很容易解决的。如果你继续跟踪堆栈，你最终会玩'*auto rehyde . js*'文件。由于该文件的名称解释了这个问题，我开始怀疑 redux-persist 配置。由于错误不可重现，因此很难调试和追溯其来源。我一直忽略它，直到第一个版本开始内部测试，那时候它变成了一个真正的痛苦。应用程序在启动前崩溃，并且“ *adb* ”记录命令完全没有用，因为屏幕上什么也没有记录。

社区唯一一次讨论这个崩溃报告是关于使用 AWS 服务的第三方库，这与我的情况无关，所以我必须隔离整个项目，从最简单的 redux 设置开始。导致该问题的原因是存储中错误配置的 redux-offline，它对状态持久性和再水合过程都产生了反作用。由于 redux-offline 推荐的安装使用 redux-persist v4(旧版本)，所以我决定使用(最新版本)v5 来获得新特性的优势，但是我忽略了最新版本的配置差异。

幸运的是，[新配置](https://gist.github.com/jarvisluong/f14872b9c7ed00bc2afc89c4622e3b55)在 redux-offline 中可用。请确保安装该依赖项并对其进行良好配置，以避免长时间忍受这种错误。以下要点显示了应用程序在调试模式下启动后运行时的终端日志。

Terminal throws error when using misconfigured redux-offline

## **2-调试构建失败**

这个崩溃记录了终端中的“*失败:构建失败，出现异常*”，并暂停了调试和发布版本的构建过程。终端显示以下内容:

Logged error in the terminal

虽然对于 web 开发人员来说，这可能不像浏览器错误那样清楚地知道哪里出错了，但是解决方法非常简单。以下要点显示了在**中列出存储库的正确方法。/android/build.gradle** 文件(您可能不一定拥有所有这些文件，但是万一列出了“google()”，请确保它在列表的顶部)

build.gradle under ./android root folder

虽然这个错误与 React-Native 无关，但我个人希望 Gradle (cli 工具)能够显示一个全面的消息，以便随时了解情况。感谢 react-native 社区帮助确定问题并提供支持。

## **3-谷歌服务版本检查**

当应用程序编译发布版本时，另一个崩溃报告记录在终端中。我们使用的第三方库越多，面临版本兼容性问题的可能性就越大。在此错误中，gradle 运行自动版本兼容性检查，并在适用时记录警告/错误，除非 Gradle 明确禁用了该检查。为此，需要将下面的命令添加到**下的 **build.gradle** 中。/android/app/** (理想情况下，在文件的最后):

```
*com.google.gms.googleservices.GoogleServicesPlugin.config.disableVersionCheck = true*
```

The error as logged in the terminal

## **4-文本字符串必须在<文本>组件**中呈现

另一个简单明了但难以发现的错误是，不小心留下了一个没有用 ***<文本>*** 组件包围的字符串。虽然这个小错误看起来并不严重，但是如果它被忽略并为生产而编译，它可能会使应用程序崩溃。

![](img/7545f7ee015a0c602dc0cb02e96d5681.png)

Emulator screenshot showing the crash report

标题错误可能看起来相当明显。然而，当错误存在于深度嵌套的组件中时，R **eact-Native 无法通知开发人员准确的错误位置**。就我个人而言，我花了大约 15 分钟，不顾一切地遍历嵌套的组件来定位缺陷。为此，在渲染时使用**速记条件表达式**时，成为牺牲品是很常见的。这里有一个简单的例子来展示:

React-Native will complain about the textArray.length for not being converted to boolean

如您所见，使用' *textArray.length* '作为条件表达式返回一个数字，并触发上面标题的错误。经过几次尝试和附带实验，我避免这种崩溃的唯一建议是始终将所有条件表达式转换为布尔数据类型。以下要点显示了相同的组件，但经过了数据类型转换:

Always convert the conditional expression to Boolean

注意 render 之前的函数，的*type 可以很好地帮助在传递某个对象之前检查/断言数据类型。通过上述调整， *render* 函数返回预期结果，并牢记安全编码实践。*

## **5-对象作为反应子对象无效。如果您打算呈现子元素的集合，请使用数组。**

虽然看起来很丑陋，但当我第一次知道 React 还不支持**异步渲染**时，我感到非常不愉快。但这不会持续太久，因为这听起来是库和组件生命周期的**重大升级，React 背后的团队将很快推出这一功能，从 v17.0 开始。显然，升级将是渐进的，因为有超过 50K 个组件由脸书维护。请[点击此处](https://reactjs.org/blog/2018/03/27/update-on-async-rendering.html)了解更多详情。**

同时，React 提出的方法非常简单。简而言之，在组件完成加载后使用异步 feed，将异步 feed 存储在将直接触发' *render'* 循环并更新视图的状态( *setState* ')中。

下面的要点展示了一个真实的用例，其中我必须呈现本地通知的列表，作为视图上的提醒。下面的代码片段使用 [react-native-firebase](https://github.com/invertase/react-native-firebase) 来生成通知。

Render async feed as recommended by React

![](img/c364175313b2f03dc2d71a2b735d275c.png)

Emulator screenshot showing the crash report

当您试图将组件渲染函数转换为 async/await 时，上面的崩溃错误就会出现。

感谢阅读。