# 惰性属性中的方法引用和 lambdas

> 原文：<https://blog.kotlin-academy.com/method-references-and-lambdas-in-lazy-properties-371dbbea857b?source=collection_archive---------3----------------------->

![](img/68a0d61855e7f102c17efccfcec1a126.png)

**这都是关于字节码的**

**TLDR**

在 Kotlin 中使用方法引用和 lambdas 时要小心，尤其是与 Android 框架创建的惰性属性和类(活动、片段、服务等)结合使用时。方法引用和 lambdas 被翻译成不同的 java 字节码，如果处理不当，这会带来麻烦。

**问题**

当我的项目突然在一个屏幕上出现奇怪的`NullPointerException` (NPE)时，我遇到了这个问题。似乎在使用辅助注入从意图中获取信息时，意图是空的(剧透:它与懒惰的表示器初始化和方法引用有关)。

**我们开始吧**

所有的代码都可以在我的[库](https://github.com/SpKiwi/lambdas)中找到。

为了说明这个问题，让我们创建一个简单的应用程序。它由两个屏幕组成。第一个屏幕是 SeekBars 可以用来根据年龄过滤人们的地方。当用户完成滑动时，他们可以打开第二个屏幕，显示基于这些标准的人(过滤器本身被传递到意图内部)。然后，如果用户点击任何一个人的名字，他们会得到一个祝酒词来问候这个特定的人。

我们将使用以下逻辑来创建屏幕:

创建这些活动的代码是相同的，除了用户正在启动的特定活动。活动也是一样的，除了一个小细节。其中一个活动将一个引用方法传递给用户适配器。另一个活动使用 lambda。

正如您所看到的，presenter 是延迟创建的，因为它需要访问在 intent 内部传递的信息(在框架完成所有与活动相关的初始化逻辑后，这些信息就可用了)。这就是辅助注入，因为参数是在运行时解析的，并且是从意图中提取的。让我们试试这两个例子。使用 lambdas 的例子工作得非常好。然而，当我们尝试使用方法引用(更简洁和更常用)时，事情就变得有趣了。

***Java . lang . nullpointerexception:试图对空对象引用调用虚拟方法“Java . io . serializable Android . content . intent . getserializableextra(Java . lang . string)”***

`UserPresenter`是延迟创建的，因此它应该在活动的`onCreate`方法之后创建，并且到那时 intent 不应该为 null。然而，在方法引用的情况下，`UserPresenter`是在此之前创建的。它实际上是在创建适配器时初始化的。这怎么可能呢？让我们深入到反编译的 java 代码中，并尝试找出答案。

首先，让我们看看使用 lambdas 的代码。

很简单。创建了一个`Function1`类的匿名实例。而`invoke`方法是当用户点击项目时将被调用的方法。但是在方法引用的情况下就有点棘手了:

匿名`Function1`也被创建。但是这里的 presenter 是作为参数传递给构造函数的。即使它很懒，这也会立即初始化它，使它尝试访问意图，结果产生 NPE。

**惰性初始化**

这也可能在其他情况下造成麻烦。初始化类有时会非常昂贵，甚至永远不会发生，这就是懒惰初始化的全部哲学。

但是由于所描述的问题，属性`b`将被立即创建，尽管这不是预期的行为。

**结论**

总而言之，要小心，避免将方法引用与惰性属性结合使用。它将防止您的应用程序进行昂贵的初始化，并使您免于与框架初始化的类(如 android 活动、片段、服务等)相关的崩溃。

# 单击👏说“谢谢！”并帮助他人找到这篇文章。

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察 Twitter](https://twitter.com/ktdotacademy) 并在媒体上关注我们。

如果您需要 Kotlin 工作室，请查看我们如何帮助您: [kt.academy](https://www.kt.academy/) 。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)