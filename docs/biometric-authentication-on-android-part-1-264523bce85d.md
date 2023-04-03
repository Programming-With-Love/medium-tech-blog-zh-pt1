# Android 上的生物认证—第 1 部分

> 原文：<https://medium.com/androiddevelopers/biometric-authentication-on-android-part-1-264523bce85d?source=collection_archive---------0----------------------->

![](img/b911131c1d399d94515164dfbc9c1c57.png)

## 为什么您的应用需要它

为了保护隐私和敏感信息，许多应用程序要求用户登录。如果您的应用程序支持传统的登录体验，它可能与图 1 中描述的过程类似。用户输入用户名和密码，应用程序将凭证发送到远程服务器，最后远程服务器返回一个`userToken`,应用程序稍后可以使用它来查询远程服务器的受限数据。无论是要求用户每次打开应用程序都要登录，还是每次安装只登录一次，图 1 都很好。

![](img/24d90ddbcfee1b83ca2d53ef83581448.png)

Figure 1: Authentication without biometrics

然而，使用图 1 所示的过程有几个缺点:

*   如果它用于银行应用程序使用的每次会话认证，那么这个过程很快就会变得繁琐，因为用户每次打开应用程序时都需要输入密码。
*   如果它用于电子邮件应用程序使用的每次安装身份验证，那么设备所有者的私人内容对任何碰巧持有该设备的人都是可见的，因为它不会验证所有者的存在。

为了帮助克服这些缺点，生物特征认证提供了许多便利，使认证过程对最终用户更容易，对开发人员更有吸引力，即使开发人员可能不需要经常登录他们的应用程序。这些优势中的关键是，使用生物认证就像轻按传感器或查看您的设备一样简单。重要的是，作为开发人员，您可以决定用户必须重新认证的频率——每天一次，每周一次，每次打开应用程序时，等等。总的来说，API surface 有许多特性使得开发者和他们的用户更容易登录。

如今，许多处理个人数据的应用程序，如电子邮件或社交网络应用程序，往往只需要在安装时进行一次性身份验证。当用户每次打开应用程序时输入用户名和密码会对用户体验产生负面影响时，这种做法就流行起来了。但是有了生物认证，安全性就不必对用户造成如此沉重的负担。即使您的应用程序通常需要一次性认证，您也可以考虑定期要求生物特征认证来验证用户的存在。周期的长短完全取决于你，开发者。

如果应用程序要求每次会话都进行身份验证(或者每 2 小时一次或每天一次等频率)。)，那么与每次都必须键入密码相比，看着设备或轻敲传感器几乎不会被注意到。如果一个应用程序只需要一次性认证，就像许多电子邮件应用程序一样，那么生物识别技术将增加一层额外的安全性，用户只需拿起并查看他们的设备。如果用户想继续保持他们的电子邮件打开，而不需要重新认证，那么他们应该有这个选择。但是对于想要更多隐私的用户来说，生物认证应该可以让他们更加放心。无论哪种方式，最终用户的成本都是微乎其微的，尤其是与额外的好处相比。

# 使用 BiometricPrompt 实现生物认证

`BiometricPrompt` API 允许您在加密和不加密的情况下实现认证。如果你正在开发一个需要更强安全系统的应用程序，比如医疗保健应用程序或银行应用程序，那么你可能希望[将你的加密密钥与生物认证](/androiddevelopers/using-biometricprompt-with-cryptoobject-how-and-why-aace500ccdb7)绑定，以便验证用户的存在。否则，为了方便用户，您可能希望实现生物认证。两种情况下的代码片段非常相似，除了对于加密实现，您将传入一个`[CryptoObject](https://developer.android.com/reference/androidx/biometric/BiometricPrompt.CryptoObject)`，而为了方便实现，您将省略`CryptoObject`参数。

**加密版本:**

```
biometricPrompt.authenticate(promptInfo, BiometricPrompt.CryptoObject(cipher))
```

虽然在上面的代码片段中，我们将一个`[Cipher](https://developer.android.com/reference/javax/crypto/Cipher)`传递给了`CryptoObject,`，但是您可以自由地传递多个替代项，比如一个`[Mac](https://developer.android.com/reference/javax/crypto/Mac)`或`[Signature](https://developer.android.com/reference/java/security/Signature)`。

**无密码对象版本:**

```
biometricPrompt.authenticate(promptInfo)
```

要在您的 Android 应用中实现生物认证，请使用`[AndroidX Biometric library](https://android-developers.googleblog.com/2019/10/one-biometric-api-over-all-android.html)`。尽管 API 处理不同的模态(指纹、面部、虹膜等。)自动地，作为一个开发者，你仍然可以通过设置`setAllowedAuthenticators()`来选择你的应用将接受的生物特征的安全级别，如下面的代码片段所示。 ***Class 3*** (原 **Strong** )表示你想要的生物特征，即解锁`Keystore`中存储的凭证(即密码术)； ***Class 2*** (之前的 **Weak** )表示你只是想解锁你的 app，而不依赖于进一步被密码术保护的凭证。有一个 ***Class 1*** ，但是和 apps 不兼容。详见[安卓兼容性定义文档](https://source.android.com/compatibility/android-cdd#7_3_10_biometric_sensors)。

```
fun createPromptInfo(activity: AppCompatActivity): BiometricPrompt.PromptInfo = BiometricPrompt.PromptInfo.Builder().*apply* **{** setAllowedAuthenticators(*BIOMETRIC_STRONG*) // Continue setting other PromptInfo attributes such as title,  subtitle, description **}**.build()
```

# 加密和每次使用授权密钥与限时密钥

***每次使用授权*密钥**是可用于执行一次加密操作的秘密密钥。因此，举例来说，如果你想执行十次加密操作，那么你必须解锁密钥十次。因此命名为 *auth-per-use* :你必须在每次使用时进行认证(即解锁密钥)。

另一方面，一个 ***有时间限制的*密钥**是一个在某个时间段内有效的秘密密钥——您可以通过将几秒钟传递给`[setUserAuthenticationValidityDurationSeconds](https://developer.android.com/reference/android/security/keystore/KeyGenParameterSpec.Builder)`来预先建立它。如果您传递给*有时间限制的*函数的秒数是-1，这是默认值，那么系统假设您想要*每次使用授权*。对于所有其他数字，我们建议三秒或更长时间，系统会遵循您设置的持续时间。要轻松创建有时间限制的密钥，请参见`[Jetpack Security](https://developer.android.com/topic/security/data)`中的`[MasterKeys](https://developer.android.com/reference/androidx/security/crypto/MasterKeys)` 类。

通常——结合前面提到的-1——您将传递一个`CryptoObject`到`BiometricPrompt.authenticate()`来请求`*auth-per-use*.` ,然而，您可以设置一个非常短的持续时间，比如 5 秒，来使用一个*有时间限制的*密钥，就好像它是一个*每次使用授权*密钥一样。这两种方法在显示用户存在方面实际上是等效的，所以如何设计应用程序的 UX 取决于你自己。

关于引擎盖下发生的事情:当您使用`CryptoObject`时，密钥仅针对指定的操作解锁。这是因为 Keymint(或 Keymaster)获得了一个带有特定操作 Id 的`HardwareAuthToken` ( `HAT`)。密钥被解锁，您只能使用它来执行您环绕`CryptoObject`的`Cipher/Mac/Signature`操作所代表的操作，并且在它再次锁定之前，您只能执行一次指定的操作——这是一个*每次使用授权密钥*。当你不使用`CryptoObject`时，送去造币厂的帽子没有`operationId`；因此，Keymint 简单地寻找一个具有有效`timestamp` ( `timestamp + time-based-key-duration > now)`)的`HAT`，并且您可以使用那个密钥直到它的时间到期——它是一个*有时间限制的*密钥。

乍一看，这听起来像是一个有时间限制的键，只要时间窗口有效，任何应用程序都可以访问它。但事实是，除了妥协的用户空间，没有人会担心某个 app *X* 使用某个 app *Y* 的按键或操作。Android 框架不会允许其他应用程序找到或初始化另一个应用程序的操作。

# 第 1 部分总结

在这篇文章中，你学到了以下内容:

*   为什么只有用户名+密码的认证是有问题的。
*   为什么在你的应用中加入生物认证是个好主意。
*   不同类型应用的设计考虑。
*   加密或不加密如何调用`BiometricPrompt`。
*   *每次使用授权*与*限时*加密密钥的区别。

在下一篇文章中，您将了解如何将正确的 ui 和逻辑整合到您的生物认证流程中。