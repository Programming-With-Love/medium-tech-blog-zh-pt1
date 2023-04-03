# Android 中的现代安全性(第三部分)

> 原文：<https://medium.com/google-developer-experts/modern-security-in-android-part-3-bea8cc6f984f?source=collection_archive---------3----------------------->

安全快速指南

这篇文章与我最近一次关于**“Android 开发者的现代安全”**的演讲有关。

以下是这个系列的博客列表:

*   [第 1 部分—加密第 1 卷](/knowing-android/modern-security-in-android-part-1-6282bcb71e6c)
*   [第 2 部分—加密第 2 卷](/knowing-android/modern-security-in-android-part-2-743cd7c0941a)
*   [**第三部分—加密第三卷**](/knowing-android/modern-security-in-android-part-3-bea8cc6f984f)
*   [第 4 部分—生物识别作为本地认证](/@dinorahto/modern-security-in-android-part-4-495655c7d4fe)
*   [第 5 部分—本地代码模块](/knowing-android/modern-security-in-android-part-5-a814a9ab7a1f)
*   [第 6 部分— SSL、TLS、安全数据层](https://ddinorahtovar.medium.com/modern-security-in-android-part-6-8b17b7a85cce)

![](img/bc68986e8c9e8fd982564cecda8ce41b.png)

**Modern Security for Android Developers**

在上一篇文章中，我们讨论了 Jetpack 安全性以及它如何处理密钥管理。

一旦你创建了万能钥匙，下一步就是使用它。在 Jetpack Security 中，您可以加密文件或共享的首选项。

## 共享首选项

共享偏好设置是在 Android 中保存数据的最原始的方式，你可以存储键和值，这些数据以 XML 文件的形式存储在应用内部目录中，用户无法访问。根据建议，开发人员应该使用标志:`[MODE_PRIVATE](https://developer.android.com/reference/android/content/Context.html#MODE_PRIVATE)`作为文件创建标志，其中创建的文件只能由调用应用程序(或所有共享相同用户 ID 的应用程序)访问

> **根据建议，Google 坚持认为开发者应该使用标志: `*MODE_PRIVATE*` *来限制其他应用程序访问数据。然而，由于你可以在 Android Studio 中使用简单的数据路径从应用程序沙箱中提取任何数据-***
> 
> data/data/<com.your.package>/shared _ prefs/yourfile . XML</com.your.package>
> 
> 我们可以看到这个文件中的任何内容，所以如果你在存储用户的令牌或个人数据，它将在几秒钟内暴露。

然而，由于您可以使用 Android Studio 中的设备监视器在**调试模式**下从应用程序中提取数据，因此很明显 Jetpack security 没有使用 SharedPreferences 和 File 对于加密正在使用:[encrypted shared preferences](https://developer.android.com/reference/androidx/security/crypto/EncryptedSharedPreferences)和 [EncryptedFile](https://developer.android.com/reference/androidx/security/crypto/EncryptedFile)

第一个方法包装 SharedPreferences 类，并使用两种方案方法自动加密密钥和值:

*   **密钥**使用确定性加密算法加密，使得密钥可以被加密和正确查找。
*   **值**使用 [AES-256 GCM](https://tools.ietf.org/html/rfc5116#section-5.2) 加密，具有不确定性。

Creation of EncryptedSharedPreferences

SharedPreferences 需要使用上下文来创建 EncryptedSharedPreferences 的实例，我们需要调用静态方法 *create()* 来创建实例。

*   masterKeyAlias:将用于本文顶部的加密的密钥
*   上下文:为了访问存储的首选项。
*   prefKeyEncryptionScheme:用于存储密钥的方案。
*   prefValueEncryptionScheme:用于存储值的方案。

对于共享偏好设置中的读写，您可以像往常一样:

# 文件

[EncryptedFile](https://developer.android.com/reference/androidx/security/crypto/EncryptedFile) 也是 FileInputStream 和 FileOutputStream 的自定义实现，为您的应用提供安全的读写流。

为了从文件流中提供安全的读写操作，安全库使用流验证加密和相关数据(AEAD ),它为数据提供验证加密，并且当要加密的数据太大而无法在一个步骤中处理时非常有用，这与 Tink 库使用的方法相同，典型的使用案例包括大型文件的加密或实时数据流的加密。

选择基础加密模式，使得可以通过解密和认证密文的一部分来快速获得部分明文，而不需要处理整个密文。

文件的缺点是加密必须一次完成，除了再次重新加密整个文件之外，没有办法修改现有的密文或向其添加内容，当然，我们有一个限制问题:

*   **明文:**可以是 0 范围内的任意长度..2 ⁸和*关联数据*可以具有范围 0 内的任意长度..2()–1 字节

有两个主要的类与整个 EncryptedFile 组件相关；第一个是我们要加密的文件和 EncryptedFile。构建者:

*   file:一个文件实例，它将一如既往地包含名称和路径
*   上下文:为了访问文件位置
*   masterKeyAlias:将用于本文顶部的加密的密钥
*   fileEncryptionScheme:用于将字节写入文件的方案。

对于文件的读写，你可以像往常一样:

【2020 年 6 月 4 日更新:
看起来这个库必须在*上实现它。pro 文件是否要发布到生产中，如果不发布，应用程序会崩溃这是 Tink 库评论的一个问题:[https://github.com/google/tink/issues/361](https://github.com/google/tink/issues/361)

```
*# Security* -keepclassmembers class * extends com.google.crypto.tink.shaded.protobuf.GeneratedMessageLite {
  *<fields>*;
}
```

这就是这篇文章的全部内容，如果你需要帮助:

我很乐意帮忙，你可以在这里找到我:
Medium:[https://medium.com/@dinorahto](/@dinorahto)
stack overflow:[https://stackoverflow.com/users/4613259/dinorah-tovar](https://stackoverflow.com/users/4613259/dinorah-tovar)

编码快乐！👩🏻‍💻