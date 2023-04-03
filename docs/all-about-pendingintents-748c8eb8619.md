# 关于未决事件的一切

> 原文：<https://medium.com/androiddevelopers/all-about-pendingintents-748c8eb8619?source=collection_archive---------1----------------------->

![](img/74154bd75aa48609f3a8dd8281cc2d2d.png)

Illustration by [Molly Hensley](https://dribbble.com/Molly_Hensley)

是 Android 框架的重要组成部分，但大多数可用的开发人员资源都专注于它们的实现细节，即“对系统维护的令牌的引用”，而不是它们的用法。

由于 Android 12 包括对待定意图的重要改变，包括需要明确决定何时可变或不可变的改变，我认为更多地讨论待定意图做什么，系统如何使用它们，以及为什么你可能偶尔想要可变的 T2 会有所帮助。

# 什么是未决事件？

一个`PendingIntent`对象包装了一个`Intent`对象的功能，同时允许你的应用程序代表你的应用程序指定另一个应用程序应该做的事情，以响应未来的动作。例如，当警报响起时，或者当用户点击通知时，可以调用包装的意图。

待定意向的一个关键方面是另一个应用程序代表你的应用程序调用意向*。也就是说，其他应用程序在调用意图时使用您的应用程序的身份。*

为了使`PendingIntent`具有与普通`Intent`相同的行为，系统使用与创建时相同的身份触发`PendingIntent`。在大多数情况下，如警报和通知，这是应用程序本身的身份。

让我们来看看我们的应用程序与`PendingIntent`配合使用的不同方式，以及我们为什么要以这些方式使用它们。

# 共同格

使用`PendingIntent`最常见也是最基本的方式是作为与通知相关联的动作:

```
val intent = Intent(applicationContext, MainActivity::class.java).apply {
    action = NOTIFICATION_ACTION
    data = deepLink
}
**val pendingIntent = PendingIntent.getActivity(
    applicationContext,
    NOTIFICATION_REQUEST_CODE,
    intent,
    PendingIntent.FLAG_IMMUTABLE
)** val notification = NotificationCompat.Builder(
        applicationContext,
        NOTIFICATION_CHANNEL
    ).apply {
        // ...
        setContentIntent(pendingIntent)
        // ...
    }.build()
notificationManager.notify(
    NOTIFICATION_TAG,
    NOTIFICATION_ID,
    notification
)
```

我们可以看到，我们正在构建一个标准类型的`Intent`来打开我们的应用程序，然后简单地将其包装在一个`PendingIntent`中，然后将其添加到我们的通知中。

在这种情况下，因为我们有一个确切的动作，我们知道我们想要执行，我们构造了一个`PendingIntent`，它不能被我们通过使用一个名为`[FLAG_IMMUTABLE](https://developer.android.com/reference/kotlin/android/app/PendingIntent#FLAG_IMMUTABLE:kotlin.Int)`的标志传递给它的应用程序修改。

在我们调用`[NotificationManagerCompat.notify()](https://developer.android.com/reference/androidx/core/app/NotificationManagerCompat#notify(java.lang.String,%20int,%20android.app.Notification))`之后，我们就完成了。系统将显示通知，当用户点击它时，在我们的`PendingIntent`上调用`[PendingIntent.send()](https://developer.android.com/reference/kotlin/android/app/PendingIntent#send)`，启动我们的应用程序。

# 更新不可变的挂起内容

你可能认为如果一个应用程序需要更新一个`PendingIntent`，它应该是可变的，但事实并非总是如此！创建`PendingIntent`的应用程序总是可以通过传递标志`[FLAG_UPDATE_CURRENT](https://developer.android.com/reference/kotlin/android/app/PendingIntent#flag_update_current)`来更新它:

```
val updatedIntent = Intent(applicationContext, MainActivity::class.java).apply {
   action = NOTIFICATION_ACTION
   data = differentDeepLink
}
// Because we're passing `FLAG_UPDATE_CURRENT`, this updates
// the existing PendingIntent with the changes we made above.
val updatedPendingIntent = PendingIntent.getActivity(
   applicationContext,
   NOTIFICATION_REQUEST_CODE,
   updatedIntent,
 **PendingIntent.FLAG_IMMUTABLE or PendingIntent.FLAG_UPDATE_CURRENT**
)
// The PendingIntent has been updated.
```

我们一会儿将讨论为什么一个人可能想要使`PendingIntent`成为可变的。

# 应用程序间 API

常见的情况不仅仅在与系统交互时有用。虽然最常见的是在执行一个动作后使用`[startActivityForResult()](https://developer.android.com/reference/android/app/Activity#startActivityForResult(android.content.Intent,%20int,%20android.os.Bundle))`和`[onActivityResult()](https://developer.android.com/reference/android/app/Activity#onActivityResult(int,%20int,%20android.content.Intent))`来[接收一个回调](https://developer.android.com/training/basics/intents/result)，但这不是唯一的方法。

想象一下，一个在线订购应用程序提供了一个 API，允许应用程序与之集成。它可能接受一个`PendingIntent`作为它自己的`extra`来启动点餐过程。订单应用程序仅在订单交付后启动`PendingIntent`。

在这种情况下，订购应用程序使用一个`PendingIntent`而不是发送一个活动结果，因为交付订单可能需要很长时间，而且强迫用户等待是没有意义的。

我们想要创建一个不可变的`PendingIntent`，因为我们不希望在线订购应用程序改变我们的`Intent`的任何东西。我们只是想让他们在订单到达时原样发送。

# 可变挂起内容

但是，如果我们是订购应用程序的开发人员，我们希望添加一个功能，允许用户键入一条消息，该消息将被发送回调用它的应用程序，那该怎么办呢？也许是为了让调用应用程序显示类似“该吃披萨了！”

这个问题的答案是使用可变的`PendingIntent`。

由于一个`PendingIntent`本质上是一个`Intent`的包装器，人们可能会认为会有一个叫做`PendingIntent.getIntent()`的方法，人们可以调用它来获取和更新被包装的`Intent`，但事实并非如此。那么它是如何工作的呢？

除了`PendingIntent`上的`send()`方法不接受任何参数之外，还有一些其他版本，包括[这个版本](https://developer.android.com/reference/kotlin/android/app/PendingIntent#send(android.content.Context,%20kotlin.Int,%20android.content.Intent))，它接受一个`Intent`:

```
fun PendingIntent.send(
    context: Context!, 
    code: Int, 
    intent: Intent?
)
```

这个`intent`参数不会替换包含在`PendingIntent`中的`Intent`，而是用来填充在`PendingIntent`创建时没有提供的包装`Intent`中的参数。

让我们看一个例子。

```
val orderDeliveredIntent = Intent(applicationContext, OrderDeliveredActivity::class.java).apply {
   action = ACTION_ORDER_DELIVERED
}
val mutablePendingIntent = PendingIntent.getActivity(
   applicationContext,
   NOTIFICATION_REQUEST_CODE,
   orderDeliveredIntent,
   PendingIntent.FLAG_MUTABLE
)
```

这个`PendingIntent`可以交给我们的在线订单 app。交付完成后，订单应用程序可以获取一个`customerMessage`并将其作为额外的意图发送回去，如下所示:

```
val intentWithExtrasToFill = Intent().apply {
   putExtra(EXTRA_CUSTOMER_MESSAGE, customerMessage)
}
mutablePendingIntent.send(
   applicationContext,
   PENDING_INTENT_CODE,
   intentWithExtrasToFill
)
```

然后，调用应用程序将在其`Intent`中看到额外的`EXTRA_CUSTOMER_MESSAGE`，并能够显示该消息。

# 声明挂起意图可变性时的重要考虑事项

⚠️在创建一个可变的`PendingIntent` **时总是**显式地设置将要在`Intent`中启动的组件。这可以用我们上面的方法来完成，通过显式设置接收它的类，但是也可以通过调用`[Intent.setComponent()](https://developer.android.com/reference/kotlin/android/content/Intent.html#setComponent(android.content.ComponentName))`来完成。

您的应用程序可能会有一个似乎更容易调用`Intent.setPackage()`的用例。如果你这样做，要非常小心**匹配多个组件**的可能性。如果可能的话，最好指定一个特定的组件来接收`Intent`。

⚠️:如果你试图覆盖用`FLAG_IMMUTABLE`创建的`PendingIntent`中的值，那么**将会无声地**失败，不加修改地交付原始包装的`Intent`。

请记住，一个应用程序总是可以更新它自己的`PendingIntent`，即使它们是不可变的。使`PendingIntent`可变的唯一原因是另一个应用程序必须能够以某种方式更新包装的`Intent`。

# 关于标志的详细信息

我们已经讨论了一些在创建`PendingIntent`时可以使用的标志，但是还有一些其他的标志需要讨论。

`[FLAG_IMMUTABLE](https://developer.android.com/reference/kotlin/android/app/PendingIntent#FLAG_IMMUTABLE:kotlin.Int)`:表示`PendingIntent`内部的意图不能被其他传递了`Intent`到`PendingIntent.send()`的应用修改。一个应用程序总是可以使用`FLAG_UPDATE_CURRENT`来修改它自己的挂起内容

在 Android 12 之前，没有这个标志的`PendingIntent`在默认情况下是可变的。

Android 6 (API 23)之前的 Android 版本上的⚠️，`PendingIntent` s 总是可变的。

🆕`[FLAG_MUTABLE](https://developer.android.com/reference/kotlin/android/app/PendingIntent#FLAG_MUTABLE:kotlin.Int)`:表示`PendingIntent`内部的意图应该允许通过合并`PendingIntent.send()`的意图参数的值来更新其内容。

⚠️ **总是**填充任何可变`PendingIntent`的包装`Intent`的`[ComponentName](https://developer.android.com/reference/android/content/ComponentName)`。否则会导致安全漏洞！

这个标志是在 [Android 12](https://developer.android.com/reference/android/app/PendingIntent#FLAG_MUTABLE) 中添加的。在 Android 12 之前，任何没有`FLAG_IMMUTABLE`标志的`PendingIntent`都是隐式可变的。

`[FLAG_UPDATE_CURRENT](https://developer.android.com/reference/kotlin/android/app/PendingIntent#flag_update_current)`:请求系统用新的额外数据更新现有的`PendingIntent`，而不是存储新的`PendingIntent`。如果`PendingIntent`没有被注册，那么这个就会被注册。

`[FLAG_ONE_SHOT](https://developer.android.com/reference/kotlin/android/app/PendingIntent#flag_one_shot)`:只允许发送一次 PendingIntent(通过`PendingIntent.send()`)。当将一个`PendingIntent`传递给另一个应用程序时，如果其中的`Intent`只能被发送一次，这可能很重要。这可能和方便有关，也可能是为了防止 app 多次执行某个动作。

🔐利用`FLAG_ONE_SHOT`防止诸如[【重放攻击】](https://en.wikipedia.org/wiki/Replay_attack)之类的问题。

`[FLAG_CANCEL_CURRENT](https://developer.android.com/reference/kotlin/android/app/PendingIntent#flag_cancel_current)`:在注册新的`PendingIntent`之前，取消现有的`PendingIntent`(如果存在的话)。如果一个特定的`PendingIntent`被发送到一个应用程序，而你想把它发送到不同的应用程序，可能会更新数据，这可能很重要。通过使用`FLAG_CANCEL_CURRENT`，第一个应用程序将不再能够调用 send，但是第二个应用程序可以。

# 接收待处理内容

有时系统或其他框架会提供一个`PendingIntent`作为 API 调用的返回。Android 11 中加入的方法`[MediaStore.createWriteRequest()](https://developer.android.com/reference/android/provider/MediaStore#createWriteRequest(android.content.ContentResolver,%20java.util.Collection%3Candroid.net.Uri%3E))`就是一个例子。

```
static fun MediaStore.createWriteRequest(
    resolver: ContentResolver, 
    uris: MutableCollection<Uri>
): PendingIntent
```

正如我们的应用程序创建的`PendingIntent`用我们的应用程序的身份运行，系统创建的`PendingIntent`用系统的身份运行。在这个 API 的例子中，这允许我们的应用程序启动一个`Activity`,它可以向我们的应用程序授予对一组`Uri`的写权限。

# 摘要

我们已经讨论过如何将一个`PendingIntent`看作是一个`Intent`的包装器，它允许系统或另一个应用程序在未来的某个时间启动一个应用程序创建的`Intent`。

我们还讨论了一个`PendingIntent`通常应该是不可变的，这样做不会阻止应用程序更新它自己的`PendingIntent`对象。除了使用`FLAG_IMMUTABLE`之外，还可以使用`FLAG_UPDATE_CURRENT`标志。

我们还讨论了我们应该采取的预防措施——确保填充被包装的`Intent`的`[ComponentName](https://developer.android.com/reference/kotlin/android/content/ComponentName)`——如果`PendingIntent`必须是可变的。

最后，我们讨论了有时系统或框架如何向我们的应用程序提供`PendingIntent` s，以便我们可以决定如何以及何时运行它们。

对`PendingIntent`的更新只是 Android 12 中旨在提高应用安全性的功能之一。点击在预览中阅读所有的[变化。](https://android-developers.googleblog.com/2021/02/android-12-dp1.html)

想做更多吗？我们鼓励您在操作系统的[新开发者预览版上测试您的应用，并且](https://developer.android.com/about/versions/12/get)[向我们提供您的体验反馈](https://developer.android.com/about/versions/12/feedback)！