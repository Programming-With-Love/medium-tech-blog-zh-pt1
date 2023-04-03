# 使用 MVVM 体系结构在 Jetpack 组合中进行 Firebase 电话认证

> 原文：<https://blog.kotlin-academy.com/firebase-phone-authentication-in-jetpack-compose-using-mvvm-architecture-258775059aa7?source=collection_archive---------0----------------------->

![](img/eba0d5031945f533c2e1201d69a0aa42.png)

Photo by [Franck](https://unsplash.com/@franckinjapan?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在这篇文章中，我将向你展示如何使用短信进行 Firebase 认证。

# 项目设置

使用 Jetpack Compose 项目模板在 Android Studio 中创建新的 Jetpack Compose 项目。接下来我们要做的是将*hilt-Android-gradle-plugin*和 *google-services* 插件添加到项目的根 *build.gradle* 文件中，并添加所需的 Firebase 和 Play services 依赖项

然后接下来，将这两个插件应用到你的 *app/build.gradle* 文件和 *kotlin-kapt 中。*

确保您将 Android Studio 项目连接到 Firebase 项目，以便将 *google-services.json* 文件下载到您的项目中。

我们要做的下一件事是编写实际的代码。在我们继续之前，请注意，使用最新版本进行 Firebase phone 身份验证需要我们设置将进行身份验证的活动。这是为了让 Firebase 知道当验证码验证完成时要返回的活动。因此，在这种情况下，我们被迫使用反模式。这就是我们项目的结构。

```
AuthService->AuthServiceImpl->AuthViewModel->PhoneLoginUI()->MainActivity
```

我们还将创建一个 ***响应*** 密封类来指定身份验证所处的状态。它可以是已初始化、正在加载、出错或成功。

我们要做的下一件事是创建 *AuthService* 接口。它将有做一个 firebase 电话认证所需的基本方法。

*signUpdateState* 变量将用于更新认证的状态。发送短信时调用 onCodeSent 方法。此时，状态会相应地更新。方法 authenticate 将用于调用 Firebase 服务来发送 SMS，当用户手动键入发送到他们电话号码的 SMS 代码时，方法 *onVerifyOtp* 将被调用。如果一切顺利，我们将调用*on verification completed*，否则我们将调用 onverificationFailed。注意，我们按照[OnVerificationStateChangedCallbacks](https://firebase.google.com/docs/reference/android/com/google/firebase/auth/PhoneAuthProvider.OnVerificationStateChangedCallbacks)中的方法对其中的一些方法进行建模。

我们的下一个类是通过 AuthServiceImpl 实现 AuthService 接口。这里我们将创建一个类型为 phoneauthprovider . OnVerificationStateChangedCallbacks 的 authCallbacks 变量，我们关注的是 OnVerificationStateChangedCallbacks 的三个重要方法，分别是 *onCodeSent* 、*onVerificationCompleted*和 *onVerificationFailed。每种方法都有其用途。下面，让我们看看每种方法的作用。*

**onCodeSent *:*** *当短信代码发送到用户设备时调用*

**onVerificationCompleted** :在用户不输入代码的情况下，从用户的设备自动检索 SMS 时调用。当他们的 SIM 卡存在于该设备上并且 SMS 代码被自动选取时，这很可能会发生。然后，我们使用此代码并完成验证过程。

**onVerificationFailed** :验证失败时调用。这可能是由于键入了错误的 SMS 代码或向 Firebase 发送了太多发送代码的请求。

下面是 *AuthServiceImpl* 类。

AuthServiceImpl 类看起来代码很多，但它相当简单。我们从构造函数注入 *FirebaseAuth* 和 *MainActivity 开始。这就是我们谈到的 ani 模式的用武之地。我们可以传递一个上下文，而不是我们的 MainActivity，并用@ActivityContext 对其进行注释，从而为我们提供 *MainActivity* (因为我们的组件将由它托管)。我们这样做只是为了在创建 *authBuilder* 变量时调用 *setActivity* 方法。如果不设置活动，我们将无法进行身份验证。不知何故，我试图传递一个上下文参数并用 *@ActivityContext* 对其进行注释，以便它充当活动，但它一直失败。我不得不通过这种方式来提供主活动。我们将在 ViewModel 中使用这个类，我们可能会担心它可能会泄漏活动，但不要担心，我们将通过创建 MainActivity 的静态实例并在 *onDestroy* 中将它设置为 null 来确保这不会在 MainActivity 中发生。*

我们使用 FirebaseAuth auth 变量来创建 *authBuilder* 变量，并在创建 *authBuilder* 时将 *authCallbacks* 设置为回调。在每个被覆盖的方法中，我们相应地调用 *authCallbacks* 的适当方法。如果代码发送成功，并且设备自动获取代码，则调用 onVerificationCompleted。我们现在使用此代码来检索使用此代码的 *PhoneAuthCredential* ，并使用此凭据将用户登录到应用程序。这就是*signInWithAuthCredential*方法的目的。如果用户的 SIM 卡在不同的设备中，他们将不得不手动键入代码，在这种情况下，我们还将调用 signInWithAuthCredential。这就是 *onVerifyOtp* 方法的目的。

当用户提交电话号码时调用的方法是 authenticate。我们在 authBuilder 上调用 *setPhoneNumber(phone)* ，然后构建一个 PhoneAuthOptions 对象，并进一步调用*phoneauthprovider . verify phonenumber(options)*。请注意 signUpState 的状态在每种情况下是如何更新的。

我们要做的下一件事是创建 AuthViewModel 类。

因为我们将大部分工作委托给了 AuthServiceImpl 类，所以 ViewModel 将相应地调用 AuthServiceImpl 中的每个方法。*号*和 *phone* I 将分别用于输入电话号码和代码。每个*on exchange*方法用于更新*电话*号码和*代码*的状态。ViewModel 将在我们的根组件中用于身份验证。

我们要做的下一件事是为身份验证编写实际的 UI 代码。我们不会只对所有代码使用一个大的可组合组件，而是会根据应用程序状态(s *ignUpState* )来分解这些组件。因此，我们将拥有用于**初始化状态**、**加载状态**和**错误**状态的用户界面。我们假设在成功状态下，我们将导航到下一个屏幕。最后，我们将拥有包含主题组件的根组件。

未初始化状态有一个供用户输入电话号码的文本字段和一个继续操作的按钮。当发送代码时，将会有为用户提供的 UI，当出现错误时，将会有错误 UI。让我们首先看看可组合的电话输入。

电话号码 UI 取*电话*号码、 *onPhoneChange* 、 *onClick* 和*on one*。我想你应该明白州政府的做法。我们已经为电话号码专门编写了一个单独的 composable，它获取电话号码，附加到 value 参数，onNumberChange，它被分配给 *OutlinedTextField* 的 *onValueChange* 方法和*on one*方法，当用户点击键盘上的 Done 按钮时将调用它们。这就是我们将 imeAction 设置为 onDone 的原因。

下一个组件是输入代码的组件。它几乎类似于电话号码，但这一次，我们没有按钮。当用户点击 Go 操作键时，我们将调用适当的方法。这就是为什么我们在这里将 imeAction 设置为 onGo。

下一个错误 UI 只是显示基于 Throwable 的错误消息。它提供了一个重启按钮，供用户重启。

最后一个可组合组件是我们的主可组合组件，它将根据状态显示上述每个可组合组件。我们将在这个可组合组件中使用 AuthViewModel。

对于状态更改，我们使用 StateFlows 和 collectAsState composable，它是 Flow 的一个扩展函数。

接下来要做的是提供各种对象，比如 FirebaseAuth、MainActivity 和 AccountService。我们将使用@Provides 注释来提供 FirebaseAuth 和 MainActivity，并将使用@Binds 来构造 AuthService。在这种情况下，我们将实现传递给方法的构造函数，返回类型是接口。我们仍然可以使用@Binds 显式地创建它。以下是模块。

接下来，我们将在 MainActivity 中创建 getInstance 方法来返回实例。我们当然会在活动的*on detail*方法中销毁它，以避免泄露活动上下文。不要忘记用@ AndroidEntryPoint 对 MainActivity 类进行注释。

此外，创建我们的 Hilt 应用程序类，并将其设置为 *AndroidManifest.xml* 中的名称。

```
@HiltAndroidApp
class MainHiltApp : Application()
```

正在清单中设置应用程序。

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">

    <uses-permission android:name="android.permission.INTERNET" />
  <application
        android:allowBackup="true"
        android:name=".ui.MainHiltApp"
        ....../>
/>
```

我希望这篇长文能帮助一些人。请在下面留下您的评论和批评。