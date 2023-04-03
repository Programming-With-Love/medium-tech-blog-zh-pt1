# 探索 Android Q:位置权限

> 原文：<https://medium.com/google-developer-experts/exploring-android-q-location-permissions-64d312b0e2e1?source=collection_archive---------0----------------------->

![](img/9ad0949cb34298080d5a2ec134ee9430.png)

> 这篇文章最初发布在[https://joebirch.co/](https://joebirch.co/)

上周，我们看到了 Android Q 测试版的发布

这个版本的 Android 带来了一系列令人兴奋的变化，我们需要让我们的应用程序做好准备。在这一组文章中，我将深入其中的每一篇，以便我们为应用程序做好充分准备！

**注:**你可以在这里找到这篇文章[的代码。](https://github.com/hitherejoe/Android-Q-Playground/tree/master)

正如 Android Q 的 [beta 发行说明](https://developer.android.com/preview/features#settings-panels)中所概述的，我们看到引入的变化之一是我们在应用程序中处理用户位置的方式——这些变化影响了前台和后台对位置的访问。这让我们的用户能够更好地控制他们希望应用程序何时能够访问他们的位置，允许他们只在应用程序当前正在使用时进行限制。在这篇文章中，我想快速探究这些变化将如何影响应用程序，以及我们需要做些什么来适应这些变化。

# 前台位置权限

可能存在这样的情况，即应用程序需要访问用户的位置，而用户发起的动作仍在继续。例如，您的应用程序可能会向用户提供导航-一旦用户从您的应用程序导航到他们的主屏幕，您将希望继续访问他们的位置，以便继续为他们提供导航服务。如果您使用前台服务来处理此功能，那么我们只需要前台访问用户位置，因为我们没有理由从后台访问用户位置(我们将在下一节讨论我们可能不使用前台服务的情况。).当您的应用程序需要访问这个位置数据时，让您的用户知道访问它的原因是很重要的。

首先，任何这种利用用户位置的服务都需要声明为位置前台服务。这可以通过在清单文件中声明服务时使用 **foregroundServiceType** 来实现。

```
<service
    android:name="ForegroundService"
    android:foregroundServiceType="location"/>
```

在我们尝试启动前台服务之前，我们需要确保我们已经获得了用户的许可。我们可以通过检查 **ACCESS_COARSE_LOCATION** 权限来做到这一点。现在，这并不是一个新的权限——事实上，从 API 级开始它就存在了。然而，我们以前只需要在应用程序清单文件中定义，现在我们必须在运行时请求该权限。您可以看到这是如何让我们的用户对如何使用该权限有了更多的控制。

```
val hasLocationPermission = ActivityCompat.checkSelfPermission(this, 
    Manifest.permission.ACCESS_COARSE_LOCATION) == PackageManager.PERMISSION_GRANTEDif (hasLocationPermission) {
    // handle location update
} else {
    ActivityCompat.requestPermissions(this,
        arrayOf(Manifest.permission.ACCESS_COARSE_LOCATION), REQUEST_CODE_FOREGROUND)
}
```

在上面的代码中，您可以看到我们首先检查我们是否拥有位置权限。如果是这样，我们可以继续处理位置流，否则，我们必须请求用户的许可。如果是这种情况，那么我们将在调用类的**onRequestPermissionsResult()**回调中接收权限状态。

当我们请求此权限时，将向我们的用户显示以下对话框:

![](img/b54456aa87addfa8ab9a04144917bade.png)

正如你所看到的，许可的意图已经很清楚了——我们的应用程序只能在前台访问他们的位置(因为应用程序正在使用)。如果用户在任何时候拒绝了此权限，我们再次请求它，那么他们将看到一个略有不同的对话框:

![](img/b7a6a633fd8f38132f0b8ce106313dd9.png)

类似于 android 中的运行时权限通常是如何工作的，我们知道显示了一个选项，用户可以请求不再被询问。由于这种功能，重要的是您只在需要时请求这种前台位置访问。这可以引导你的用户在当前的上下文中为什么需要权限，而不是给人一种毫无理由的感觉。

# 后台位置权限

当我们的应用程序是后台时，当访问用户位置时，事情会有一点不同。我们首先需要向我们的清单文件添加一个新的权限，这是**访问 _ 背景 _ 位置**权限。尽管这是在清单中声明的，但用户仍然可以随时撤销它。

```
<manifest>
  <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
  **<uses-permission android:name="android.permission.ACCESS_BACKGROUND_LOCATION" />**
</manifest>
```

请注意，这里我们不需要将 **foregroundServiceType** 服务类型添加到我们的服务声明中，这是因为我们不需要在应用程序外部运行的瞬时权限——这个后台权限已经赋予了我们的应用程序这样做的能力。

如上所述，权限需要在运行时由用户授予。因此，在我们尝试从后台访问用户的位置之前，我们需要确保我们拥有用户这样做所需的权限。我们可以通过检查**ACCESS _ BACKGROUND _ LOCATION**权限来做到这一点。您可以再次看到这是如何让我们的用户对如何使用此权限有更多的控制，并避免后台位置访问被滥用。

我们检查权限的代码可能看起来像这样:

```
val hasForegroundLocationPermission = ActivityCompat.checkSelfPermission(this, 
    Manifest.permission.ACCESS_COARSE_LOCATION) == PackageManager.PERMISSION_GRANTEDif (hasForegroundLocationPermission) {
    val hasBackgroundLocationPermission = ActivityCompat.checkSelfPermission(this, 
        Manifest.permission.ACCESS_BACKGROUND_LOCATION) == PackageManager.PERMISSION_GRANTED if (hasBackgroundLocationPermission) {
        // handle location update
    } else {
        ActivityCompat.requestPermissions(this,
            arrayOf(Manifest.permission.ACCESS_BACKGROUND_LOCATION), REQUEST_CODE_BACKGROUND)
    }
} else {
    ActivityCompat.requestPermissions(this,
        arrayOf(Manifest.permission.ACCESS_COARSE_LOCATION,
            Manifest.permission.ACCESS_BACKGROUND_LOCATION), REQUEST_CODE_BACKGROUND)
}
```

您会注意到这段代码非常类似于我们为前台位置权限定义的检查。在这里，我们检查这两种权限，如果用户出于某种原因拒绝我们的后台访问，我们就可以在位置检索中进行回退。

当我们请求此权限时，将向我们的用户显示以下对话框:

![](img/2ab8de27ef24fe10732b350a35c775f2.png)

许可的意图已经很清楚了——我们的应用程序将能够在后台访问他们的位置。如果用户在任何时候拒绝了此权限，我们再次请求它，那么他们将看到一个略有不同的对话框:

![](img/96b49596c55ac16dc806dfa5ed00c5f8.png)

类似于 android 中的运行时权限通常是如何工作的，我们知道显示了一个选项，用户可以请求不再被询问。由于这种功能，重要的是您只在需要时请求这种后台位置访问。这可以引导你的用户在当前的上下文中为什么需要权限，而不是给人一种毫无理由的感觉。

从这篇文章中我们可以看到，Android Q 改变了我们的应用程序使用位置权限的方式。我们的应用程序将不再能够从服务中自由访问用户位置，无论是在前台还是后台。为了支持向后兼容性，如果您的应用程序不面向 Q，那么如果声明了粗略或精细权限，将添加**ACCESS _ BACKGROUND _ LOCATION**权限。类似地，在运行时请求这两者中的任何一个也将授予该权限。

这些变化让我们的用户能够更好地控制应用程序如何访问他们的位置，使我们能够在创建应用程序时考虑到用户的隐私和安全。对这些位置变化有什么问题吗？欢迎在评论中联系我们！

[](https://twitter.com/hitherejoe) [## 乔·伯奇(@hitherejoe) |推特

### 乔伯奇的最新推文(@hitherejoe)。Android Lead @Buffer。谷歌开发专家为@Android，@GooglePay &…

twitter.com](https://twitter.com/hitherejoe)