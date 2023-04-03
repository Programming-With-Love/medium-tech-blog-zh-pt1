# Android 11 中的包可见性

> 原文：<https://medium.com/androiddevelopers/package-visibility-in-android-11-cc857f221cd9?source=collection_archive---------1----------------------->

![](img/de14acf6439dcd8e0639d02dd4c04976.png)

Illustration by [Molly Hensley](https://dribbble.com/Molly_Hensley)

在 Android 10 和更早的版本中，应用程序可以使用类似`queryIntentActivities()`的方法查询系统中已安装应用程序的完整列表。在大多数情况下，这远远超出了应用程序实现其功能所需的访问范围。随着我们对隐私的持续关注，我们将在 Android 11 上引入应用程序如何查询和与同一设备上安装的其他应用程序交互的变化。特别是，我们正在为特定设备上安装的应用程序列表提供更好的范围访问。

为了对设备上已安装应用的访问提供更好的问责，默认情况下，针对 Android 11 (API 级别 30)的应用将看到已安装应用的过滤列表。为了访问更广泛的已安装应用程序列表，应用程序可以指定他们需要查询和直接交互的应用程序的信息。这可以通过在 Android 清单中添加一个`<queries>`元素来实现。

对于大多数[常见场景](https://developer.android.com/preview/privacy/package-visibility#use-cases-not-affected)，包括任何以`startActivity()`开头的隐含意图，你不需要改变任何东西！对于[其他场景](https://developer.android.com/preview/privacy/package-visibility-use-cases)，比如直接从用户界面打开一个特定的第三方应用程序，开发者必须明确列出应用程序包名称或[意图过滤器签名](https://developer.android.com/preview/privacy/package-visibility#intent-signature)，如下所示:

```
<manifest package="com.example.game">
  <queries>
    <!-- Specific apps you interact with, eg: -->
    <package android:name="com.example.store" />
    <package android:name="com.example.service" /> <!--
         Specific intents you query for,
         eg: for a custom share UI
    -->
    <intent>
      <action android:name="android.intent.action.SEND" />
      <data android:mimeType="image/jpeg" />
    </intent>
  </queries>
  ...
</manifest>
```

如果您使用[自定义标签](https://developers.google.com/web/android/custom-tabs)打开 URL，您可能会调用`resolveActivity()`和`queryIntentActivities()`来启动一个非浏览器应用程序(如果该 URL 有可用的话)。在 Android 11 中，有一种更好的方式来做到这一点，这避免了查询其他应用程序的需要:意图标志。当您使用这个标志调用`startActivity()`时，如果浏览器已经启动，将会抛出一个`ActivityNotFoundException`。发生这种情况时，您可以在自定义选项卡中打开 URL。

```
try {
  val intent = Intent(ACTION_VIEW, Uri.parse(url)).apply {
    // The URL should either launch directly in a non-browser app
    // (if it’s the default), or in the disambiguation dialog addCategory(CATEGORY_BROWSABLE)
    flags = FLAG_ACTIVITY_NEW_TASK or FLAG_ACTIVITY_REQUIRE_NON_BROWSER
  } startActivity(intent)
} catch (e: ActivityNotFoundException) {
  // Only browser apps are available, or a browser is the default app for this intent
}
```

在极少数情况下，您的应用程序可能需要查询设备上安装的所有应用程序或与之交互，而与它们包含的组件无关。为了允许你的应用程序看到所有其他已安装的应用程序，Android 11 引入了`[QUERY_ALL_PACKAGES](https://developer.android.com/preview/privacy/package-visibility#all-apps)`权限。在即将到来的 Google Play 政策更新中，寻找需要`QUERY_ALL_PACKAGES`权限的应用程序的指南。

当目标是 API 等级 30 并且在你的应用中添加一个`<queries>`元素时，使用最新发布的 Android Gradle 插件。很快，我们将发布旧版本 Android Gradle 插件的更新，以增加对该元素的支持。你可以在[开发者文档](https://developer.android.com/preview/privacy/package-visibility)中找到更多关于包可见性的信息和用例。