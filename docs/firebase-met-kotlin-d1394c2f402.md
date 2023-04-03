# Firebase 遇到了 kotlin！

> 原文：<https://medium.com/google-developer-experts/firebase-met-kotlin-d1394c2f402?source=collection_archive---------2----------------------->

Kotlin 当然是一种美丽的语言，大多数 android 开发人员都喜欢它，而且在很多情况下，kotlin 特性可以节省应用程序开发的时间，并提高应用程序的质量。Firebase APIs 简洁明了，但 kotlin 可以通过使其对开发人员友好，甚至比目前更简洁来进一步改进它。

![](img/97e45dc70b76105a56ea185e278e3479.png)

ktx 扩展从一组 Android 库开始，现在这些库也扩展到了 firebase。Firebase kotlin 扩展提供了一种调用 API 的 kotlin 方式，看起来比以前更加整洁。虽然第一个问题可能是如何将它们添加到您的 android 项目中，但是对于这个，像往常一样添加库并在末尾追加-ktx，如下所示

```
implementation 'com.google.firebase:firebase-firestore-ktx:21.4.1'
```

**分机可用于？** 目前有很多支持 kotlin 扩展的库，下面是它们的列表

```
firebase-database-ktx
firebase-firestore-ktx
firebase-functions-ktx
firebase-inappmessaging-ktx
firebase-inappmessaging-display-ktx
firebase-remote-config-ktx
firebase-storage-ktx
firebase-common-ktx
```

这意味着如果你正在使用上述服务，那么你可以切换到 ktx 库来利用 kotlin 的特性。

Firebase Firestore Extensions 是一个实时 NoSQL 数据库，它提供了很多好处，如果你正在使用 ktx，那么接下来的事情将会改变

```
implementation 'com.google.firebase:firebase-firestore-ktx:21.4.1'
```

在代码中，以下内容将会改变

```
val firestore = FirebaseFirestore.getInstance() 
**becomes** val firestore = Firebase.firestoreval classObject = snapshot.get("fieldPath", MyClass::class.java)
**becomes** val classObject = snapshot.get<MyClass>("fieldPath")val classObject = snapshot.toObject(MyClass::class.java)
**becomes** val classObject = snapshot.toObject<MyClass>()FirebaseFirestore.getInstance()
    .collection("**collectionId**").document("**docId"**)
    .addSnapshotListener **{** documentSnapshot, _ **->** 
    **}
becomes** Firebase.*firestore*.collection(**"collectionId"**).document(**"docId"**)
    .addSnapshotListener **{** documentSnapshot, firebaseFirestoreException **->

    }**
```

**实时数据库扩展** 这是一个实时 NoSQL 数据库，可以在几秒钟内将数据无缝传输到所有连接的设备

```
implementation 'com.google.firebase:firebase-database-ktx:19.2.1'
```

在代码中，以下内容将会改变

```
val database = FirebaseDatabase.getInstance(FirebaseApp.getInstance("myApp"))
**becomes** val database = Firebase.database(Firebase.app("myApp"))val myObject = snapshot.getValue(MyClass::class.java)
**becomes** val myObject = snapshot.getValue<MyClass>()val database = FirebaseDatabase.getInstance(app, url)
**becomes** val database = Firebase.database(app, url)
```

**远程配置扩展** 

```
implementation 'com.google.firebase:firebase-config-ktx:19.1.3'
```

在代码中，以下内容将会改变

```
val remoteConfig = FirebaseRemoteConfig.getInstance()
**becomes** val remoteConfig = Firebase.remoteConfigval configSettings = FirebaseRemoteConfigSettings.Builder()
        .setMinimumFetchIntervalInSeconds(3600)
        .setFetchTimeoutInSeconds(60)
        .build()
remoteConfig.setConfigSettingsAsync(configSettings)
**becomes** val configSettings = remoteConfigSettings {
    minimumFetchIntervalInSeconds = 3600
    fetchTimeoutInSeconds = 60
}
remoteConfig.setConfigSettingsAsync(configSettings)
```

**动态链接扩展** 

```
implementation 'com.google.firebase:firebase-dynamic-links-ktx:19.1.0'
```

在代码中，以下内容将会改变

```
val dynamicLink = FirebaseDynamicLinks.getInstance().createDynamicLink()
        .setLink(Uri.parse("https://www.example.com/"))
        .setDomainUriPrefix("https://example.page.link")
        .setAndroidParameters(
                DynamicLink.AndroidParameters.Builder("com.example.android")
                        .setMinimumVersion(16)
                        .build())
        .setIosParameters(
                DynamicLink.IosParameters.Builder("com.example.ios")
                        .setAppStoreId("123456789")
                        .setMinimumVersion("1.0.1")
                        .build())
        .setGoogleAnalyticsParameters(
                DynamicLink.GoogleAnalyticsParameters.Builder()
                        .setSource("orkut")
                        .setMedium("social")
                        .setCampaign("example-promo")
                        .build())
        .setItunesConnectAnalyticsParameters(
                DynamicLink.ItunesConnectAnalyticsParameters.Builder()
                        .setProviderToken("123456")
                        .setCampaignToken("example-promo")
                        .build())
        .setSocialMetaTagParameters(
                DynamicLink.SocialMetaTagParameters.Builder()
                        .setTitle("Example of a Dynamic Link")
                        .setDescription("This link works whether the app is installed or not!")
                        .build())
        .buildDynamicLink()**becomes**val dynamicLink = Firebase.dynamicLinks.dynamicLink {
    link = Uri.parse("https://www.example.com/")
    domainUriPrefix = "https://example.page.link"
    androidParameters("com.example.android") {
        minimumVersion = 16
    }
    iosParameters("com.example.ios") {
        appStoreId = "123456789"
        minimumVersion = "1.0.1"
    }
    googleAnalyticsParameters {
        source = "orkut"
        medium = "social"
        campaign = "example-promo"
    }
    itunesConnectAnalyticsParameters {
        providerToken = "123456"
        campaignToken = "example-promo"
    }
    socialMetaTagParameters {
        title = "Example of a Dynamic Link"
        description = "This link works whether the app is installed or not!"
    }
}
```

我希望你会喜欢 kotlin 扩展，它减少了编写代码的工作量，使代码变得整洁干净。