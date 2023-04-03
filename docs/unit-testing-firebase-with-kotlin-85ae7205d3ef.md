# 使用 Kotlin 对 Firebase 进行单元测试

> 原文：<https://blog.kotlin-academy.com/unit-testing-firebase-with-kotlin-85ae7205d3ef?source=collection_archive---------0----------------------->

![](img/df200881bee02d81c08ec48bc507e9ca.png)

Photo by [Louis Tsai](https://unsplash.com/@louis993546?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/kotlin?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

现代移动编程实践，以及更广泛的编程实践，非常强调测试驱动的开发。如果你想用 Kotlin 开发一个 Android 应用程序，最好记住 TDD，因为它允许你的应用程序被分解成可分离的可测试组件——使得开发测试更容易，发现应用程序中的错误更容易。

如果应用程序要有任何形式的 CRUD 功能，后端的一个好选择是 Firebase。Firebase 具有与 Android Studio 的本机集成，如果您没有后端经验，它非常容易设置，这使它成为初学者和有经验的程序员的绝佳选择。

考虑到 TDD，您可能想要测试使用 Firebase 的方法，以确保一切正常工作。

Firebase 测试的常见解决方案是使用非生产数据库，并对该数据库执行实际的读写操作，以确保一切正常( [Firebase 文档](https://firebase.google.com/docs/projects/multiprojects))。

通过这种方法，您可以像平常一样使用 Firebase 只是在一个不同的数据库上，以确保您不会在生产中搞砸任何事情。

但是如果您不想使用非生产数据库呢？要么您是一个不想维护两个数据库的开发人员，要么您大部分时间都无法访问互联网？

在这篇博客中，我将向您展示如何使用`Mockito`来模拟不同的 Firebase 函数并执行本地单元测试。

# 设置

我将使用一个示例场景展示如何模拟 Firebase。假设您有一个带有登录功能的 CRUD 应用程序。围绕 Firebase 有一个名为`LogInModel`的包装器类(在以后修改后端时的最佳实践)。

`LogInModel`有一个我们想要测试的函数`LogIn(email, password)`。`LogIn`只是使用方法`signInWithEmailAndPassword`调用 Firebase 类`FirebaseAuth`。下面是`LogInModel`中的`LogIn`方法的基本情况:

在上面的代码片段中，您可能会注意到`observer`对象。`observer`是我将在下面定义的`LogInListener`类型。

`LogInListener`是一个简单的`interface`，其他类可以遵循它来知道登录是成功还是失败。`LogInListener`简单来说就是这样定义的:

所以我们正在尝试用我们的单元测试来确保`logIn`做它应该做的事情——用 Firebase 登录用户。对于这个例子，很明显，它做了它应该做的事情，因为代码很短，但是在大型项目中，测试是必要的。

# 测试

我们准备做一个名为`LogInModelTest`的单元测试(在 Android Studio 中称为“*测试*”)。我们的测试类将使用`Mockito`来模拟我们用来测试类功能的 firebase 对象。

我将一节一节地分解`LogInModelTest`,这样我就可以详细了解这个类是如何构造的。

这是我们测试文件的开始。

在第一行中，您会注意到我们的测试实现了`LogInListener`——这使得获得我们正在测试的`logIn`方法的结果变得很容易。因为我们在文件的末尾有那个`implements`，你会注意到我有来自`LogInListener`接口的方法。

然后我们有了`@Mock private val mAuth: FirebaseAuth`，它是我们正在模仿的 Firebase 对象。

`LogInModel`是我们将要进行单元测试的对象。

您看到的两个`Task`将覆盖 Firebase 登录的结果。它允许我们测试失败的登录和成功的登录。

三个`private const val`用于更新`LogInListener`调用的结果。你可以用一个枚举来代替它，这是最好的做法，但是为了弄清楚我要做什么，我把它们作为单独的变量。它们与`logInResult`一起使用。

这是我们的`@Before`设置方法。

简单地设置我们的模拟对象。

这里我定义了`successTask`和`failureTask`。现在[任务](https://firebase.google.com/docs/reference/android/io/fabric/sdk/android/fabric/services/concurrency/Task)是一个接口，因此我定义了这么多函数(“…”包含更多函数)。我在这里只强调重要的。

在方法的最后，我们简单地初始化其余的变量。

现在谈谈测试方法！

我们正在测试成功登录。

我们首先设置我们的电子邮件/密码组合。

然后，我们使用`Mockito.when`来确保当我们调用我们的`FirebaseAuth`对象时，我们将返回`successTask`(一个表示登录成功的任务)并且不会得到一个空指针异常或其他意外行为。

我们在被测试的类上调用`logIn`，然后断言我们的登录尝试成功了。`logInResult`是由我之前提到的界面设置的。

如果您想测试失败，只需检查调用是否导致了失败，并使用`failureTesk`和`Mockito.when`调用。下面是这样做的一个例子:

最后，我们继续学习类中最后剩下的代码——接口方法！

这里，如果登录尝试成功，我们简单地设置`logInResult = SUCCESS`，否则我们将其设置为`FAILURE`。这使得上面的 assert 语句可以工作。

就是这样！虽然它肯定比使用一个单独的非生产数据库多一点代码，但它肯定可以在不需要额外数据库的情况下对 Firebase 进行单元测试。如果你把代码分成更小的块，你会发现没有一个是特别复杂的。祝测试好运！

为了便于参考，下面是一个片段中的整个类:

如果你做到了这一步，这里有一个🎁作为我感激的象征！

# 单击👏说“谢谢！”并帮助他人找到这篇文章。

了解卡帕头最新的重大新闻。Academy，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察 Twitter](https://twitter.com/ktdotacademy) 并在 medium 上关注我们。

如果您需要 Kotlin 工作室，请查看我们如何帮助您: [kt.academy](https://kt.academy/) 。

[![](img/935962ffca48419fed5580c8073bffe5.png)](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)