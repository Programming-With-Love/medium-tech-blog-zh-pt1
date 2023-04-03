# 基于 Jetpack 安全的 Android 数据加密

> 原文：<https://medium.com/androiddevelopers/data-encryption-on-android-with-jetpack-security-e4cb0b2d2a9?source=collection_archive---------0----------------------->

![](img/6aa0e62bc10980f69aaf4032835b5196.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

**更新:2021 年 4 月 21 日:** Jetpack 安全现[稳定 1.0.0](https://developer.android.com/jetpack/androidx/releases/security#1.0.0) ！
**更新:2021 年 12 月 2 日:**Jetpack Security[1 . 1 . 0–Alpha-03](https://developer.android.com/jetpack/androidx/releases/security#security-crypto-1.1.0-alpha03\)发布，增加了对 API level 21+的支持，其中包括重新设计的 MasterKey 实现。

你试过在你的应用程序中加密数据吗？作为一名开发人员，您希望保证数据的安全，并掌握在打算使用的一方手中。但如果你像大多数 Android 开发者一样，你没有专门的安全团队来帮助正确加密你的应用数据。通过搜索网络来学习如何加密数据，你可能会得到几年前的答案，并提供不正确的例子。

[Jetpack Security](https://developer.android.com/topic/security/data.md)(JetSec)加密库为加密文件和 SharedPreferences 对象提供了抽象。该库在使用安全且众所周知的[密码原语](https://github.com/google/tink/blob/master/docs/PRIMITIVES.md)的同时，促进了 [AndroidKeyStore](https://developer.android.com/training/articles/keystore) 的使用。使用`EncryptedFile`和`EncryptedSharedPreferences`允许您在本地保护可能包含敏感数据、API 密钥、OAuth 令牌和其他类型秘密的文件。

为什么要在应用中加密数据？Android 不是从 5.0 开始，[默认加密用户数据分区](https://source.android.com/security/encryption)的内容吗？的确如此，但是在一些使用案例中，您可能需要额外的保护级别。如果你的 app 使用[共享存储](https://developer.android.com/training/data-storage/shared)，你就要加密数据。在应用主目录中，如果您的应用处理敏感信息，包括但不限于个人身份信息(PII)、健康记录、财务详情或企业数据，您的应用应加密数据。如果可能的话，我们建议您将此信息与生物特征相关联，以获得额外的保护。

Jetpack 安全基于谷歌的开源跨平台安全项目 Tink。如果您需要一般加密、混合加密或类似的东西，Tink 可能是合适的。Jetpack 安全数据结构与 Tink 完全兼容。

# 密钥生成

在我们开始加密您的数据之前，了解如何保护您的加密密钥是很重要的。Jetpack Security 使用一个*主密钥*，用于加密所有用于加密操作的子密钥。JetSec 在`MasterKeys`类中提供了一个推荐的默认主密钥。该类使用一个基本的 AES256-GCM 密钥，该密钥是在 AndroidKeyStore 中生成和存储的。AndroidKeyStore 是一个容器，它将加密密钥存储在 TEE 或 StrongBox 中，使它们很难被提取。子项存储在一个可配置的`SharedPreferences`对象中。

首先，我们在 Jetpack 安全中使用了`AES256_GCM_SPEC`规范，这是为一般用例推荐的。AES256-GCM 是对称的，在现代设备上通常速度很快。

对于需要更多配置或处理非常敏感的数据的应用程序，建议构建您的`KeyGenParameterSpec`，选择对您的使用有意义的选项。带`[BiometricPrompt](https://developer.android.com/jetpack/androidx/releases/biometric)`的限时密钥可以提供额外的保护，防止根设备或受损设备。

重要选项:

*   `userAuthenticationRequired()`和`userAuthenticationValiditySeconds()` 可用于创建一个有时间限制的密钥。有时间限制的密钥需要使用`BiometricPrompt`对对称密钥的加密和解密进行授权。
*   `unlockedDeviceRequired()`设置一个标志，有助于确保在设备未解锁的情况下无法访问密钥。此标志在 Android Pie 及更高版本上可用。
*   使用`setIsStrongBoxBacked()`，在更强大的独立芯片上运行加密操作。这对性能有轻微的影响，但更安全。它在一些运行 Android Pie 或更高版本的设备上可用。

**注意:**如果您的应用程序需要在后台加密数据，您不应使用有时限的密钥或要求设备解锁，因为没有用户在场，您将无法完成此操作。

Configuration requiring user authentication using BiometricPrompt

# 解锁有时间限制的密钥

如果您的密钥是使用以下选项创建的，您必须使用`BiometricPrompt`来授权设备:

*   `userAuthenticationRequired`是真的
*   `userAuthenticationValiditySeconds` > 0

用户通过身份验证后，密钥会在“有效秒数”字段中设置的时间内解锁。AndroidKeystore 没有查询密钥设置的 API，因此您的应用程序必须跟踪这些设置。您应该在向用户显示对话框的活动的`onCreate()`方法中构建您的`BiometricPrompt`实例。

解锁限时钥匙的生物计量提示码

# 加密文件

Jetpack Security 包括一个`EncryptedFile`类，它消除了加密文件数据的挑战。与`File`类似，`EncryptedFile`提供了一个用于读取的`FileInputStream`对象和一个用于写入的`FileOutputStream`对象。文件使用[流式 AEAD 加密，它遵循 OAE2 定义](https://github.com/google/tink/blob/master/docs/PRIMITIVES.md#streaming-authenticated-encryption-with-associated-data)。数据被分成块，并使用 AES256-GCM 加密，这样就不可能重新排序。

# 加密共享的首选项

如果您的应用程序需要保存键-值对——比如 API 键——JetSec 提供了`EncryptedSharedPreferences`类，该类使用您已经习惯的 SharedPreferences 接口。

密钥和值都是加密的。密钥使用 [AES256-SIV-CMAC](https://tools.ietf.org/html/rfc5297) 加密，提供确定性密文；值使用 AES256-GCM 加密，并绑定到加密密钥。该方案允许安全地加密密钥数据，同时仍然允许查找。

# 更多资源

[fileloker](https://github.com/android/security-samples/tree/master/FileLocker)是 Android Security GitHub 示例页面上的一个示例应用。这是一个很好的例子，说明了如何使用 Jetpack 安全来使用文件加密。

有关最新进展，请查看这里。如有疑问，欢迎在 Twitter 上联系我( [@jonmarkoff](https://twitter.com/jonmarkoff) )。

加密快乐！