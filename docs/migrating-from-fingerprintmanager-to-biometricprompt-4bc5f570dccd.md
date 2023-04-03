# 从 FingerprintManager 迁移到 BiometricPrompt

> 原文：<https://medium.com/androiddevelopers/migrating-from-fingerprintmanager-to-biometricprompt-4bc5f570dccd?source=collection_archive---------2----------------------->

![](img/b3e889324da1641fb12bc9b821106675.png)

*Android 框架*和*安全*团队最近发布了 [*AndroidX 生物特征库*](https://developer.android.com/jetpack/androidx/releases/biometric) ，这是一个支持库，取代了 API 之前的所有迭代。该库使得在 *Android 10* (API 等级 29)中宣布的所有功能可以一直追溯到 *Android 6* (API 等级 23)。支持库具有以下主要优势:

*   开发人员不再需要在他们的代码中预测不同的 API 级别，因为该库在幕后处理所有的 API 匹配。例如，支持库无缝地在 API 级别 23 到 27 上使用`[FingerprintManager](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager)`,在 API 级别 28 及以上使用`[BiometricPrompt](https://developer.android.com/reference/androidx/biometric/BiometricPrompt.html)`。
*   开发人员不再需要创建自己的身份验证 UI。该库提供了一个标准且熟悉的用户界面，可匹配用户的生物认证形式，如指纹或面部认证。
*   开发人员可以通过一个方法调用来检查设备是否支持生物认证。

这个库提供了大量的便利，你可以通过`BiometricPrompt`和`[BiometricManager](https://developer.android.com/reference/androidx/biometric/BiometricManager.html)`类来访问它们！

如果你的应用程序仍然没有使用 AndroidX 生物特征库，这篇文章将告诉你如何迁移。

# 提高认证安全性

根据 *Android 兼容性定义文件(Android CDD)* ，生物识别传感器可以根据传感器的欺骗和冒名顶替接受率等因素分为 [**强**或**弱**](https://source.android.com/compatibility/android-cdd#7_3_10_biometric_sensors) 。这意味着您的代码不需要确定生物认证有多强 OEM 实现为您完成了这项工作。

因此，为了提高认证安全性，应用程序在使用生物认证时通常会指定一个`[CryptoObject](https://developer.android.com/reference/androidx/biometric/BiometricPrompt.CryptoObject.html)`。这是因为`CryptoObjects`与`[KeyStore](https://developer.android.com/reference/java/security/KeyStore)`合作提供了额外的安全保障。例如，一个银行应用程序可能需要一个`CryptoObject`来解密敏感数据。使用`CryptoObject`将保证使用*强*生物特征来验证你的应用。此外，使用`CryptoObject`还能让您更加放心，因为它与`KeyStore`集成在一起，后者能够抵御对受损系统(如根设备)的攻击。

因此，一个应用程序有两种可能的实现:

1.  使用 a `CryptoObject`进行生物认证**(推荐)。**
2.  生物认证**没有**有`CryptoObject`。

# 使用加密技术迁移到生物计量提示

通常，应用程序必须创建以下对象来支持`FingerprintManager`工作流:

*   一个登录`[Button](https://developer.android.com/reference/android/widget/Button)`，用户点击它启动指纹认证 UI。
*   一个`[DialogFragment](https://developer.android.com/reference/android/app/DialogFragment)`及其相关的布局文件。这代表您的自定义指纹用户界面。
*   允许用户使用账户密码而非`FingerprintManager`进行验证的可选逻辑。您可能出于不同的原因提供此选项:可能设备不支持指纹验证，可能用户忘记设置指纹验证，或者可能用户只是更喜欢帐户密码验证。

要从`FingerprintManager`迁移到`BiometricPrompt`，完成以下步骤:

## 1.删除自定义指纹用户界面

马上，你可以扔掉你的指纹 UI 的`FragmentDialog`和布局文件。你不需要它们。AndroidX 生物特征库有一个标准化的用户界面。

## 2.将 Gradle 依赖项添加到您的应用程序模块中

在撰写本文时，该库的最新版本是 1.0.0。

## 3.创建 BiometricPrompt 的实例

你应该在你的`Activity` / `Fragment`生命周期的早期实例化`BiometricPrompt`，最好是在`onCreate()`或`onCreateView()`中。这是`FingerprintManager`和`BiometricPrompt`的一个关键区别。在调用`authenticate()`的时候实例化`FingerprintManager`是可以的。但是对于向适当的活动发送回调的`BiometricPrompt`，比方说在配置改变的情况下，您必须在需要调用`authenticate()`之前对其进行一点实例化。

## 4.生成 PromptInfo 对象

`[BiometricPrompt.PromptInfo](https://developer.android.com/reference/androidx/biometric/BiometricPrompt.PromptInfo)`是使用`BiometricPrompt` API 进行认证的必需参数。它为提示提供了重要的说明，例如是否需要显式用户确认(注意:显式确认是默认行为，如果 API 正用于支付或交易，则应始终应用-对于与应用程序或帐户登录一起使用，显式确认可设置为 false 以实现更简化的体验)。`BiometricPrompt.PromptInfo`还允许您的应用程序通过在提示上设置标题、副标题和其他描述性文本来为正在进行的交易提供额外的上下文。

您的`BiometricPrompt.PromptInfo`可能看起来像这样:

回想一下`createBiometricPrompt()`中的这个片段，它向用户展示了`loginWithPassword()`选项:

## 5.创建一个加密对象

由于您是从包含一个`CryptoObject`的`FingerprintManager`实现中迁移过来的，因此在旧代码的某个时候，您创建了一个`[FingerprintManager.CryptoObject()](https://developer.android.com/reference/android/hardware/fingerprint/FingerprintManager.CryptoObject.html)`的实例来传递给`fingerprintManager.authenticate(crypto, cancel, flags, callback, handler)`。当迁移到`BiometricPrompt`时，你仍然可以使用相同的参数来创建你的`CryptoObject`，除了你的`CryptoObject`现在是一个`[BiometricPrompt.CryptoObject](https://developer.android.com/reference/androidx/biometric/BiometricPrompt.CryptoObject.html)`。

## 6.设置您的视图。OnClickListener

既然您已经有了一个`BiometricPrompt`的实例，并且已经构建了一个`PromptInfo`对象，那么您就可以要求用户进行身份验证了。通常，当用户点击按钮时，会发生以下两种情况之一:

*   如果设备支持生物认证，那么用户会看到*生物库*的标准用户界面。
*   否则，用户将获得自定义的“使用帐户密码登录”用户界面。此外，即使用户获得了标准的*生物特征库*用户界面，他们仍然有机会通过点击用户界面中的否定按钮来使用特定于应用程序的帐户密码登录。

与使用`FingerprintManager` API 不同，您可以通过一个方法调用来检查设备是否支持生物认证:`BiometricManager.from(context).canAuthenticate()`。这个单一的方法调用检查设备上是否有可用的生物特征硬件，用户是否注册了模板，或者用户是否启用了生物特征认证。如果这三个都不是真的，则不能显示生物特征提示。

这就是加密迁移的全部内容。一旦认证成功，API 将调用`[onAuthenticationSucceeded()](https://developer.android.com/reference/androidx/biometric/BiometricPrompt.AuthenticationCallback.html#onAuthenticationSucceeded(androidx.biometric.BiometricPrompt.AuthenticationResult))`回调方法，您的应用程序就可以继续显示私有数据了！

# 迁移到没有加密的 BiometricPrompt

如果您正在从一个不包含`CryptoObject`的`FingerprintManager`实现进行迁移，那么您所要做的就是跳过上面我们谈到的创建`CryptoObject`的*步骤 5* 。类似于用空的`CryptoObject`调用`fingerprintManager.authenticate()`，只需调用没有`CryptoObject`参数的`biometricPrompt.authenticate()`。

这个例子使用了`setNegativeButtonText()`，它允许您为一个按钮指定标签，该按钮取消生物认证并在按下时消除提示。当这种情况发生时，`[onAuthenticationError(errCode, errString)](https://developer.android.com/reference/androidx/biometric/BiometricPrompt.AuthenticationCallback.html#onAuthenticationError(int,%20java.lang.CharSequence))`收到错误代码`[ERROR_NEGATIVE_BUTTON](https://developer.android.com/reference/androidx/biometric/BiometricPrompt.html#ERROR_NEGATIVE_BUTTON)`，允许您的应用程序在按钮被按下时提供自定义行为。例如，一种可能的用途是将按钮标记为“使用帐户密码”，并在按下时显示应用程序内的替代登录选项。

另一种方法是`setDeviceCredentialAllowed(true)`，用户可以选择使用他们的**设备密码、PIN 或模式**进行身份验证，而不是生物特征凭证。如果使用`setDeviceCredentialAllowed(true)`，生物特征库将处理认证。注意`setDeviceCredentialAllowed(true)`与`CryptoObject`不兼容。你也可以调用`[setNegativeButtonText()](https://developer.android.com/reference/androidx/biometric/BiometricPrompt.PromptInfo.Builder.html#setNegativeButtonText(java.lang.CharSequence))`或`[setDeviceCredentialAllowed(true)](https://developer.android.com/reference/androidx/biometric/BiometricPrompt.PromptInfo.Builder.html#setDeviceCredentialAllowed(boolean))`，**，但不能同时调用**。

在`onAuthenticationSucceeded()`回调方法中，您可以像在旧代码中一样继续操作。因为没有`CryptoObject`，你可能不需要对`AuthenticationResult`做任何事情。

# 摘要

我们完事了。总之，要从`FingerprintManager`迁移到`BiometricPrompt`，您需要做以下工作:

1.  扔掉你自定义的指纹认证 UI。
2.  在你的`onCreate()`或`onCreateView()`生命周期方法中实例化`BiometricPrompt`，同时传递给它一个`BiometricPrompt.AuthenticationCallback()`的实现
3.  构建一个`PromptInfo`对象。
4.  创建一个`BiometricPrompt.CryptoObject`，如果您想使用推荐的方法将加密技术包含在您的生物认证工作流程中。
5.  调用`BiometricPrompt.authenticate()`。

如需进一步阅读，请参见[开发者指南](https://developer.android.com/training/sign-in/biometric-auth)和 [API 参考](https://developer.android.com/reference/androidx/biometric/BiometricPrompt)。

编码快乐！