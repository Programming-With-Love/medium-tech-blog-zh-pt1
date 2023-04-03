# 让我们明确一下我们的意图(-过滤器)

> 原文：<https://medium.com/androiddevelopers/lets-be-explicit-about-our-intent-filters-c5dbe2dbdce0?source=collection_archive---------2----------------------->

![](img/74154bd75aa48609f3a8dd8281cc2d2d.png)

Illustration by [Molly Hensley](https://dribbble.com/Molly_Hensley)

Android 12 将会有一个重要的变化，它可以提高应用和平台的安全性。这一变化影响到**所有** **针对 Android 12** 的应用。

带有声明意图过滤器的活动、服务和广播接收器现在必须明确声明它们是否应该被导出。

❗️ **如果你的应用程序因为这些错误信息中的一条而失败，很可能与这个变化有关。**

> `Installation did not succeed.
> The application could not be installed: INSTALL_FAILED_VERIFICATION_FAILURE
> List of apks:
> [0] ‘…/build/outputs/apk/debug/app-debug.apk’
> Installation failed due to: ‘null’`

或者

> `INSTALL_PARSE_FAILED_MANIFEST_MALFORMED: Failed parse during installPackageLI: /data/app/vmdl538800143.tmp/base.apk (at Binary XML file line #…): com.example.package.ActivityName: Targeting S+ (version 10000 and above) requires that an explicit value for android:exported be defined when intent filters are present”`

# 修复

修复这些错误的方法是将属性`android:exported`添加到任何在应用程序的`AndroidManifest.xml`文件中声明了`<intent-filter>`的`<activity>`、`<activity-alias>`、`<service>`或`<receiver>`组件中。

⚠️ **不要**只需将`android:exported=”true”`添加到所有这些组件中！检查每个包含`<intent-filter>`定义的组件，并问自己:“我希望安装在某人设备上的任何应用程序能够启动这个组件吗？”

这个问题的答案取决于应用程序做什么，其他应用程序如何与它交互，以及其他应用程序特定的考虑因素。以下是一些常见意图过滤器的常见示例，以及建议值和选择原因的解释:

带`<category android:name=”android.intent.category.LAUNCHER” />`的活动:`android:exported=”true”`

这可能是你的应用程序的`MainActivity`，由于 Android 上的启动器可能是一个常规应用程序，这个活动必须被导出，否则启动器将无法启动它。

带`<action android:name=”android.intent.action.VIEW” />`的活动:`android:exported=”true”`

此活动负责处理来自其他应用程序的“打开方式”操作。

带`<action android:name=”android.intent.action.SEND” />`或`<action android:name=”android.intent.action.SEND_MULTIPLE”/>`的活动:`android:exported=”true”`

此活动负责处理其他应用程序与其共享的内容。参见[从其他应用程序接收简单数据](https://developer.android.com/training/sharing/receive)。

与`<action android:name=”android.media.browse.MediaBrowserService” />` : `android:exported=”true”`的服务

如果这是一项向其他应用程序公开应用程序媒体库的服务，则应将其导出，以允许其他应用程序连接和浏览该媒体库。该服务可能会直接或间接扩展`[MediaBrowserServiceCompat](http://mediabrowserservicecompat)`。如果没有，则可能没有必要将其导出。

使用`<action android:name=”com.google.firebase.MESSAGING_EVENT” />`的服务:`**android:exported=”false”**`

这是 Firebase Cloud Messaging 将调用的服务，并且应该扩展`FirebaseMessagingService`。这个服务不应该*被导出，因为无论它是否被导出，Firebase 都能够启动组件。有关更多信息，请参见[在 Android 上设置 Firebase 云消息客户端应用](https://firebase.google.com/docs/cloud-messaging/android/client#manifest)。*

带`<action android:name=”android.intent.action.BOOT_COMPLETED” />`的接收器:`**android:exported=”false”**`

因为系统正在将该动作传递给广播接收机，所以无论它是否被导出，它都可以这样做。

# 背景

在 Android 12 之前，声明了意图过滤器的组件(活动、服务和广播接收器**仅**)被自动导出

默认情况下，会导出此活动:

```
<activity android:name=".MainActivity">
    <intent-filter>
        <action android:name="android.intent.action.MAIN" />
        <category android:name="android.intent.category.LAUNCHER" 
    </intent-filter>
</activity>
```

此活动未导出:

```
<activity android:name=".MainActivity" />
```

虽然这似乎是一个合理的默认，但这里的错误可能会使应用程序容易受到攻击。例如，假设我们的应用程序有一个播放视频的活动:

```
<activity android:name=”.PlayVideoActivity” />
```

后来，我们发现我们从许多地方显式地链接到这个活动，因此，为了让我们的应用程序更松散地耦合，我们更改了我们的活动，以包括一个意图过滤器，允许系统选择活动:

```
<activity android:name=”.PlayVideoActivity”>
    <intent-filter>
        <action android:name=”android.intent.action.VIEW” />
        <data
            android:mimeType=”video/*”
            android:scheme=”content” />
    </intent-filter>
</activity>
```

突然我们有了一个问题。我们的 activity 只设计为供我们的应用程序内部使用，现在已导出！

一旦我们瞄准 Android 12，系统将通过要求我们明确 android:exported 的值来阻止这一点。在这种情况下，我们不希望它被导出，所以我们可以设置该属性，并保持我们的应用程序安全。

```
<activity
    android:name=”.PlayVideoActivity”
    android:exported=”false”>
    <intent-filter>
        <action android:name=”android.intent.action.VIEW” />
        <data
            android:mimeType=”video/*”
            android:scheme=”content” />
    </intent-filter>
</activity>
```

# TL；速度三角形定位法(dead reckoning)

Android 12 中的一个重要变化是提高安全性。以该版本为目标的应用程序需要明确声明任何`activity`、`activity-alias`、`service`或广播`receiver`的`android:exported`属性的值，这些属性在其`AndroidManifest.xml`文件中包含一个意图过滤器。否则，应用程序将无法安装。

仔细考虑将该属性设置为什么值，如果有疑问，请选择设置`android:exported=”false”`。

有关意图和意图过滤器的更多信息，请参见[接收隐式意图](https://developer.android.com/guide/components/intents-filters#Receiving)。

有关其他安全和隐私更新的更多信息，请参见本页。

关于 Android 12 中所有变化的更多信息，请查看[开发者预览版 1 的博客帖子！](https://android-developers.googleblog.com/2021/02/android-12-dp1.html)