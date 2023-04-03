# Android:面向初学者的应用清单概述

> 原文：<https://medium.com/globant/android-application-manifest-overview-for-beginners-22ca92c8498c?source=collection_archive---------0----------------------->

![](img/2e35f5071fa8978bd647417157060d71.png)

Image source : Google

> **Android 中的 AndroidManifest.xml 文件是什么？**

*   清单文件向 Android 构建工具、Android 操作系统和 Google Play 描述了关于您的应用的基本信息。
*   它负责通过提供权限来保护应用程序访问任何受保护的部分。

![](img/982d656e18dbe72b6632361803e5a700.png)

Image source : Google

*   这个文件包含关于你的应用级包和不同组件活动、服务、内容提供者等的信息。

> **Android manifest . XML 文件示例**

```
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.myapp"
    android:versionCode="1"
    android:versionName="1.0" > <application  
        android:icon="@drawable/ic_launcher"  
        android:label="@string/app_name"  
        android:theme="@style/AppTheme" >        <activity  
            android:name=".MainActivity"  
            android:label="@string/title_activity_main" >  
            <intent-filter> 

                <action android:name="android.intent.action.MAIN" />  

                <category          android:name="android.intent.category.LAUNCHER" />              </intent-filter>  
        </activity>  
    </application>
</manifest>
```

*   现在让我们浏览一下 AndroidManifest.xml 的**元素**:

(一).***<manifest>:*manifest**是 AndroidManifest.xml 文件的根元素。它必须包含一个`[<application>](https://developer.android.com/guide/topics/manifest/application-element)`元素，并指定`xmlns:android`和`package`属性。

*   属性:`xmlns:android , package , android:sharedUserId , android:targetSandboxVersion , android:sharedUserLabel, android:versionCode , android:versionName , android:installLocation`

(b).***<应用> :*** 该元素包含几个**子元素**，声明 activity 等应用组件。这是应用程序的声明。

*   这些属性中的许多属性(例如`icon`、`label`、`permission`、`process`和`allowTaskReparenting`)为组件元素的相应属性设置默认值。

(c .)***<activity>:***这是 application 的子元素，表示必须在 AndroidManifest.xml 文件中定义的活动。它有许多属性，如标签，名称，主题，启动模式等。

*   这个元素可以包含:`[<intent-filter>](https://developer.android.com/guide/topics/manifest/intent-filter-element)` `, [<meta-data>](https://developer.android.com/guide/topics/manifest/meta-data-element)` `, [<layout>](https://developer.android.com/guide/topics/ui/multi-window#layout)`

(d.) ***<意向过滤器> :* 意向过滤器**是描述活动、服务或广播接收器可以响应的意向类型的活动子元素。

*   这个元素可以包含:`[<category>](https://developer.android.com/guide/topics/manifest/category-element)` `, [<data>](https://developer.android.com/guide/topics/manifest/data-element)`

(e.) ***<动作> :*** 为意图过滤器添加一个动作。意图过滤器必须至少有一个操作元素。

*   如果意图过滤器中没有`<action>`元素，则过滤器不接受任何`[Intent](https://developer.android.com/reference/android/content/Intent)`对象。
*   属性:`android:name`

(f.) ***<类别> :*** 将类别名称添加到意图过滤器

```
**Syntax:** 
<category android:[name](https://developer.android.com/guide/topics/manifest/category-element#nm)="*string*" />
```

以下是一些有助于深入理解属性的参考链接:

*   [https://developer . Android . com/guide/topics/manifest/action-element](https://developer.android.com/guide/topics/manifest/action-element)
*   [https://developer . Android . com/guide/topics/manifest/activity-element](https://developer.android.com/guide/topics/manifest/activity-element)

快乐阅读:)