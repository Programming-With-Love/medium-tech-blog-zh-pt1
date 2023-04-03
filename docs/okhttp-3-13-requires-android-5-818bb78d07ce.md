# OkHttp 3.13 要求安卓 5+版

> 原文：<https://medium.com/square-corner-blog/okhttp-3-13-requires-android-5-818bb78d07ce?source=collection_archive---------0----------------------->

![](img/e8e91389e5562a7e67d6ded807312b1e.png)

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

今天我们发布 OkHttp 3.13。通过此次更新，我们将从以下方面提升项目要求:

*   Android 2.3+/API 9+(2010 年 12 月发布)
*   Java 7+(2011 年 7 月发布)

对此:

*   Android 5.0+/API 21+(2014 年 11 月发布)
*   Java 8+(2014 年 3 月发布)

淘汰旧设备是一项重大变革，我们不会轻易放弃！我想解释一下我们为什么要这样做，我们正在做什么来最大限度地减少中断，以及如何升级。

# 为什么是 Android 5+为什么是现在？

TLS 是使 HTTPS 呼叫安全、保密和经过身份验证的机制。OkHttp 知晓五个版本: *SSLv3* (1996 年)、 *TLSv1* (1999 年)、 *TLSv1.1* (2006 年)、 *TLSv1.2* (2008 年)、 *TLSv1.3* (2018 年)。我们在 2014 年放弃了对 *SSLv3* 的支持，以回应[狮子狗攻击](https://googleonlinesecurity.blogspot.ca/2014/10/this-poodle-bites-exploiting-ssl-30.html)。

现在是时候放弃 TLSv1 和 TLSv1.1，让 TLSv1.2 成为互联网新的最低标准了。10 月 15 日，我们在[谷歌](https://security.googleblog.com/2018/10/modernizing-transport-security.html)、 [Mozilla](https://blog.mozilla.org/security/2018/10/15/removing-old-versions-of-tls/) 、[微软](https://blogs.windows.com/msedgedev/2018/10/15/modernizing-tls-edge-ie11/)和[苹果](https://webkit.org/blog/8462/deprecation-of-legacy-tls-1-0-and-1-1-versions/)的同事宣布，从 2020 年初开始，他们的浏览器将要求 *TLSv1.2* 或更高版本。

谷歌在 Android 5.0 中增加了对 *TLSv1.2* 的支持。Oracle 在 Java 8 中添加了它。在 OkHttp 3.13 中，我们要求主机平台内置对 *TLSv1.2* 的支持。

# Android 4.x 呢？

谷歌的[分发仪表板](https://developer.android.com/about/dashboards/)显示，2018 年 10 月访问 Play Store 的设备中约有 11%运行的是 Android 4.x，我们已经创建了一个分支 [OkHttp 3.12.x](https://github.com/square/okhttp/tree/okhttp_3.12.x) ，来支持这些设备。如果我们遇到任何严重的错误或安全问题，我们将反向移植修复和发布。我们计划在 2020 年 12 月 31 日之前维持这一分支机构。

如果你真的需要 Android 4.x 上的 *TLSv1.2* ，那是可以的！Ankush Gupta 已经写了一个全面的指南，解释了如何让 Google Play 服务做到这一点。即使你遵循这个过程，你仍然应该在 Android 4.x 设备上使用 OkHttp 3.12.x。

# 我如何升级？

确认你的项目的`minSdkVersion`至少是 21，并且你的 Android Gradle 插件版本至少是 3.2。然后在您的`build.gradle`中使用我们新的 Maven 坐标:

```
dependencies {
  implementation "com.squareup.okhttp3:okhttp:3.13.1"
  ...
}
```

您还需要将 Java 版本设置为 1.8 或更高版本。我们现在在源代码中使用 Java 8 特性(耶 [lambdas！](https://github.com/square/okhttp/pull/4549/files))这使得 Android 的编译器能够[处理它](https://jakewharton.com/androids-java-8-support/):

```
android {
  compileOptions {
    sourceCompatibility JavaVersion.VERSION_1_8
    targetCompatibility JavaVersion.VERSION_1_8
  }
  ...
}
```

默认情况下，更新还需要具备 *TLSv1.2* 功能的 web 服务器。如果你的网络服务器过时了，HTTPS 对它们的调用将会失败，并出现一个`SSLException`。如有必要，您可以配置 OkHttp 3.13 以允许 *TLSv1* 和 *TLSv1.1* 连接:

```
OkHttpClient client = new OkHttpClient.Builder()
    .connectionSpecs(Arrays.asList(ConnectionSpec.COMPATIBLE_TLS))
    .build();
```

如果你发现自己正在这样做，请注意:在 2020 年初，网络浏览器将停止连接到你的网络服务器！我们建议尽可能提高服务器的能力，而不是降低客户端的要求。

我们在项目 wiki 上记录了 [TLS 配置变更](https://github.com/square/okhttp/wiki/TLS-Configuration-History)。

# 谢谢

我们开发人员花费大量的时间和精力来保持依赖关系最新。我们应该庆祝这项工作！我们的用户信任他们的应用程序，信任他们最重要的东西，包括私人谈话、金钱和家庭照片。我们通过尽最大努力保护他们的数据来兑现这种信任。

我很感谢你花时间更新。这可能不容易，但很重要。