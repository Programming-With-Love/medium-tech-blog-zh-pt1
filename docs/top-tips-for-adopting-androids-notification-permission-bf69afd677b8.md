# 采用 Android 通知权限的顶级技巧

> 原文：<https://medium.com/androiddevelopers/top-tips-for-adopting-androids-notification-permission-bf69afd677b8?source=collection_archive---------0----------------------->

![](img/c280add9ed9ca9f364efa932861a872b.png)

# 概观

过多的通知是全球用户的数字福利问题。在 **Android 13** 中，我们通过引入新的[运行时权限](https://developer.android.com/about/versions/13/changes/notification-permission)来帮助用户收回对通知体验的控制权。用户必须将此权限授予应用程序，该应用程序才能将通知发布到通知抽屉，包括与前台服务相关的通知。

对于许多应用来说， [Android 的官方文档](https://developer.android.com/about/versions/13/changes/notification-permission)应该有足够的信息和指导来装载你的应用来处理新的许可。如果你正在寻找技巧来改善你的应用程序的用户体验**在**你的目标是 Android 13 或测试你的应用程序的集成与许可没有闪存不同的操作系统版本到你的设备上，那么你来对地方了！

# 如果您的应用程序正在显示用户可见的活动，使用目标 SDK <= 32

In Android 13, when your app creates its first notification channel, the [**请求上下文的用户会看到通知权限提示**](https://developer.android.com/about/versions/13/changes/notification-permission#new-apps) 。这意味着，如果您的应用程序在启动时或在后台运行时创建了第一个频道，那么用户在启动您的应用程序后会立即看到提示。

这种突然的提示可能会破坏你的应用程序的用户旅程，并可能增加权限拒绝率。这是因为新用户还不熟悉你的应用程序，他们可能会对授权访问他们的个人信息感到不舒服。出于这些原因，我们强烈建议在上下文中请求权限[——也就是说，当他们导航到适当的特性时。上下文中的含义因应用程序而异，您可以在运行时权限](https://developer.android.com/about/versions/13/changes/notification-permission#wait-to-show-prompt)的[文档中找到一些想法。](https://developer.android.com/about/versions/13/changes/notification-permission#wait-to-show-prompt)

虽然当你的应用针对 Android 13(将 targetSdkVersion 设置为 33)时，请求上下文中的权限要容易得多，但针对旧版本 Android 的应用可以通过更改它们创建第一个通知通道的时间来帮助操作系统请求上下文中的权限。如前所述，Android 建议你在用户处于情境中时创建你的第一个频道。

你也可以开始起草你的应用程序的[教育用户界面](https://developer.android.com/training/permissions/requesting#explain)来解释为什么你的应用程序需要访问通知。我们的[研究](https://developer.android.com/training/permissions/requesting#explain)显示，解释为什么需要特定权限的应用程序往往会获得更高的选择加入率。

如果您的应用程序为新用户提供了入职流程，您可以选择在您的应用程序创建其第一个通知渠道并导致通知权限提示出现之前，先显示教育用户界面。请用你的应用程序测试一下，从一个新用户的角度来验证这个时间是否合理。

# 测试通知权限

我们知道你们中的许多人都在寻求更简单的方法来[测试你的应用程序与通知权限功能的集成](https://developer.android.com/about/versions/13/changes/notification-permission#test)！我们很高兴宣布一些新的 adb 命令，可以帮助简化您的测试过程。

第一步:将最新版本的 Android 13 闪存到您的设备中。

第二步:[在你的设备上设置 adb](https://developer.android.com/studio/command-line/adb#Enabling) 。

步骤 3:在下面找到您想要测试和运行相关 adb 命令的场景，以设置您的应用程序的通知权限状态。

**注意:**确保将“com.name.app”更改为您自己的应用程序包名称。

1.  运行 Android 13 的设备上新安装的应用程序

```
adb shell pm revoke com.name.app android.permission.POST_NOTIFICATIONSadb shell pm clear-permission-flags com.name.app android.permission.POST_NOTIFICATIONS user-setadb shell pm clear-permission-flags com.name.app android.permission.POST_NOTIFICATIONS user-fixed
```

2.现有应用(在传统操作系统中，用户启用了**通知**

```
adb shell pm grant com.name.app android.permission.POST_NOTIFICATIONSadb shell pm set-permission-flags com.name.app android.permission.POST_NOTIFICATIONS user-setadb shell pm clear-permission-flags com.name.app android.permission.POST_NOTIFICATIONS user-fixed
```

3.现有应用(传统操作系统中用户禁用了**通知**

```
adb shell pm revoke com.name.app android.permission.POST_NOTIFICATIONSadb shell pm set-permission-flags com.name.app android.permission.POST_NOTIFICATIONS user-setadb shell pm clear-permission-flags com.name.app android.permission.POST_NOTIFICATIONS user-fixed
```

# 发送通知时的一般最佳实践

**发送通知前验证权限:**

在您尝试[发送通知](https://developer.android.com/guide/topics/ui/notifiers/notifications)之前，请确认用户已启用通知。您可以通过首先调用[notification manager # areNotificationsEnabled()](https://developer.android.com/reference/android/app/NotificationManager#areNotificationsEnabled())API 来进行检查。用户将其设备升级到 Android 13 后，除了设置页面中的通知切换之外，API 还会反映用户对通知权限的响应。检查许可也有助于你评估你的特性依赖于许可的影响(例如退出率)。

**处理权限拒绝:**

如果通知对你的应用程序是必要的，而用户拒绝了许可，你可以考虑[帮助用户理解](https://developer.android.com/training/permissions/requesting#handle-denial)拒绝的含义，比如显示一个教育用户界面。在此解释中，提及他们无法再从您的应用接收通知。

**增强对渠道的支持:**

继续[将通知分类到通道](https://developer.android.com/guide/topics/ui/notifiers/notifications#ManageChannels)，因为它允许用户选择他们希望从您的应用程序接收的通知类型(而不是完全禁用您的应用程序的通知)并控制通知的视觉/听觉行为。此外，使用有意义的通道名称和描述来帮助用户理解通道中包含的类型通知。