# 在移动应用程序中测试 SSL 固定

> 原文：<https://medium.com/globant/testing-ssl-pinning-in-a-mobile-application-2dcac9ab3d0c?source=collection_archive---------0----------------------->

# 摘要

本文描述了 SSL 固定的概念及其在移动项目中实现的重要性，以防止专用网络上的应用程序使用代理拦截 TLS/SSL 请求。

# 描述

OWASP 将 SSL 固定定义为*“用户和开发人员在他们的应用程序中发送和接收数据时，期望端到端的安全性，尤其是受 VPN、SSL 或 TLS 保护的通道上的敏感数据。虽然控制 DNS(域名系统)和 CA(证书颁发机构)的组织已将大多数威胁模型中的风险降低到微不足道的水平，但用户和开发人员受制于他人的 DNS 和公共 CA 的层级，他们面临着不小的风险"*。在移动应用程序的情况下，应该考虑的一个优先事项是，对应用程序使用的服务的调用或请求不应该被拦截。如果用户(在特定情况下是恶意用户)可以捕获应用程序对后端/服务的请求以及它传递给它的参数，攻击者就可以操纵所发出的请求，方法是操纵请求中发送的参数、请求头、您发出的请求的类型、将不同类型的参数传递给那些在前端层而不是后端层验证的预期参数，等等。

恶意用户捕获应用程序请求(即使它们受到安全通道的保护)的方法之一是，通过他/她自己的 CA 安装签名证书，并配置一个使用该证书捕获应用程序发送的请求的代理。通过依赖代理证书，即使请求通过安全通道，也有可能捕获请求，并公开发送到应用程序使用的服务的请求的参数、标头、URL 和类型。避免这类重大威胁的一种方法是在应用程序中实现 SSL 固定。

也就是说，SSL pin 接受主机或服务的证书或公钥散列；它可以在开发时添加到应用程序中，并在应用程序每次发送请求时与发布的服务进行比较。它也可以添加到应用程序和服务之间的第一次握手中。最好选择第一种方法，因为预加载证书或带外公钥通常意味着攻击者无法发现 pin。如果证书或公钥是在第一次遇到时添加的，它可能会锁定攻击者的证书。

SSL 固定利用用户与组织或服务之间的现有关系来帮助做出更好的安全相关决策。因为您已经有了关于服务器或服务的信息，所以您不需要依赖通用的机制来解决密钥分发问题。也就是说，您不需要通过 DNS 获得名称/地址，也不需要通过 CA 分配获得链接和状态。

每次应用程序向主机或服务发送请求时，它都会将应用程序中的证书与从服务收到的证书进行比较，如果它们匹配，则表示它们之间存在安全连接，否则应用程序不会发送数据，并将通知用户连接错误。可以对叶证书、中间证书或根证书进行锁定。Mathew Dolan 在他的 [Android Security: SSL Pinning 文章](/@appmattus/android-security-ssl-pinning-1db8acb6621e)中广泛解释了每种类型、差异和实现。

以下概念证明(PoC)是一个关于 SSL 固定重要性的示例。该示例用登录表单捕获请求，以纯文本形式公开凭据，并通过代理工具修改它们。第二个示例包括 SSL Pinning 的实现，它防止登录表单被拦截。此 PoC 包括 Burp Suite Community Edition，用于捕获应用程序请求，其中包含一个代理，可以设置该代理来执行捕获。

# 概念开发的证明

## **拓扑**

![](img/c7ec4fd522c90c842058862dc42bc93a.png)

*Image 1: Topology Context Diagram*

## **代理设置**

对于这个特定的案例，使用了 Burp Suite Community Edition 工具。这个工具带有一个集成的代理功能。不同的工具可用于此目的，例如:Charles、MitM Proxy、ZAP 等。

对于此 WLAN，Burp 代理的 IP 在端口 8080 上设置为 10.30.6.134。这些配置必须在装有要测试的应用程序的手机上进行设置。

![](img/68b9304e5bef0c380edf4cc81949c80f.png)

## **移动配置**

对于 WLAN，将在其中测试应用程序的客户端/电话(iPhone 7 v13.3.1)的私有 IP 设置为 10.30.6.133。然后，我们还需要用上一步中定义的 IP 来配置电话使用的代理。

![](img/4b1ab4e71e8ea4a7c3e9ff62ad906c13.png)

*Image 3: Mobile IP Topology configuration*

![](img/ef9d224e94dff6fcf8fea1caa399ef1d.png)

*Image 4: Mobile Proxy Topology configuration*

如果您使用的是 Android Studio 模拟器，则必须在设置菜单和代理选项卡中的模拟器扩展控件中配置代理。在本例中，IP 设置如拓扑所示。

![](img/0d19d69623f5e2ef624f18d5ce82e1e9.png)

Image 5: Android Studio emulator proxy configuration

## **手机证书配置**

Burp 证书(Portswigger CA)必须设置为“完全信任”,以便 Burp 拦截请求。安装证书的所有步骤都可以在官方 Portswigger 文档中找到，这些文档适用于 [iOS](https://portswigger.net/support/installing-burp-suites-ca-certificate-in-an-ios-device) 和 [Android](https://portswigger.net/support/installing-burp-suites-ca-certificate-in-an-android-device) 。

![](img/365079363e7afffd9748d7f09e259e10.png)

*Image 6: Mobile certificate configuration full trust*

![](img/f787cff959dbe1faf6eefaa2ca6315cc.png)

*Image 7: Mobile certificate configuration verified*

# 捕获请求

在这一步，我们可以开始测试和拦截请求。请注意，在拦截请求时，代理捕获受 SSL 保护的 URL 的请求，即使它通过安全通道传输，也可以以纯文本形式看到请求的参数。

## 登录不正确的凭据测试

第一个测试是对一个应用程序进行的，该应用程序有一个发送电子邮件和密码的表单，用于验证应用程序内部的用户。在这个测试中，为了以纯文本形式显示请求中发送的参数，我们使用表单中不正确的凭证对其进行了测试，并在 Burp 上捕获了这些凭证。

*   **通过移动应用发送凭证**

![](img/30f3a3445bacdd2f577adb43b29318a2.png)

Image 8 Mobile application login test

*   **代理拦截请求凭证不正确**

请求在 Burp 代理中被拦截，我们可以看到公开的端点的 URL，并且在参数中发送的用户凭证以纯文本显示。这些参数被发送到应用程序后端，并且可以在具有 Burp 的 repeater 工具上操作。

![](img/0268d9302ad58be679b3bef603ac884e.png)

*Image 9: Proxy request intercepted incorrect credentials*

*   **代理历史请求不正确的凭证**

对于这个测试，我们还可以在 proxy HTTP history 选项卡中看到请求响应的状态。

![](img/d1bac08094edee06afbe08de90fe8f98.png)

*Image 10: Proxy history request incorrect credentials*

*   **代理历史响应凭证不正确**

我们还可以看到从端点收到的请求响应。

![](img/c5af92cbbf5fbd1129749bccd59fea38.png)

*Image 11: Proxy history response incorrect credentials*

## **使用正确的凭证登录测试**

下一个例子是使用相同应用程序表单的正确凭证，并截取应用程序后端服务的响应。

*   **代理历史请求正确的凭证**

![](img/6e4a76c5be89a5e0a29b12372559cc97.png)

*Image 12: Proxy history request correct credentials*

*   **代理历史响应正确凭证**

正如您在这个测试中看到的，响应显示了一个 access_token 和关于用户的更多信息。这些信息和第一次测试中显示的信息可以用来自动对该应用程序后端进行暴力攻击，或者对其进行 DDoS 攻击。

![](img/eac90ba5d09472621e463f952e52aecf.png)

*Image 13: Proxy history request correct credentials*

# SSL 固定实施

## ios

Swift 5.2 为 iOS 应用程序提供了一种相对简单的实现 SSL 锁定的方法。必须下载终端的证书。下载证书后，必须将证书作为文件放在 resource 文件夹中，以便可以在希望调用它的项目类中识别它。在这个例子中，它被称为“certificade-file-name”。der 扩展。比较现有证书并解决来自服务的“挑战”。每次应用程序发送请求时，它都使用开发的方法。在本例中是“urlSession”方法。

![](img/21ff58ef35ce79f59c843d00aae504e9.png)

*Image 14: Swift code SSL pinning, credits to Ivan Rapoport*

## 机器人

在 Android 的情况下，不需要下载整个证书，只需要包含散列并与来自服务的散列进行比较。在 extras 部分，您可以找到 bash 代码来提取公共证书的散列。OkHttp3 库在客户端有一个方法，处理每个请求的散列验证。在下一个 Kotlin 代码示例中，它在构建 okHttpClientBuilder 对象的部分执行锁定，创建一个单独的 CertificadePinner 类型的对象，其中包含作为 *sha256* 的散列。CertificadePinner 对象作为 okHttpClientBuilder 对象的 certificatePinner 方法中的参数发送:

```
//OkHttp3 Client
val okHttpClientBuilder = OkHttpClient.Builder()
 .authenticator(CustomAuthenticator.getInstance(context))

//SSL Pinning
val certPin = CertificatePinner.Builder()
 .add(BuildConfig.PATTERN, “sha256/**YOUR-HASH-HERE**”)
 .build()okHttpClientBuilder.certificatePinner(certPin)
```

更多 Android 中的实现方法:

[](/@appmattus/android-security-ssl-pinning-1db8acb6621e) [## Android 安全性:SSL 锁定

### 在 Android 应用中使用 SSL 很容易，但是确保连接的安全却是另一回事。一个…

medium.com](/@appmattus/android-security-ssl-pinning-1db8acb6621e) 

# 实现 SSL 固定的测试请求捕获

实现完成后，是时候再次检查前面部分中完成的过程并比较结果了。

首先，在 Burp 仪表板上的事件日志中可以看到，当在应用程序上发送请求时，连接 SSL 无法完成，因为它在证书交换时失败。

![](img/895d6b510b25fb694960d5a55aba737b.png)

*Image 15: Burp certificate negotiation error*

同样，如果您有可能使用 Logcat 调试应用程序，对于 Okhttp3 库，您可以看到一个错误，通知您一个 SSL 握手异常，即已配置的证书 pin 与应用程序和端点之间当前交易的 pin 不匹配。换句话说，库无法找到在当前请求中收到的证书的路径，并且不允许在证书对之间建立连接。

![](img/f0760433ad38ccedc65b1afaa161e6c3.png)

*Image 16: Android logcat Okhttp3 pinning error*

# 风险

应用程序中 SSL 固定的错误或无效配置会暴露移动应用程序对其后端的调用。恶意用户可以将此作为攻击媒介。

SSL 牵制是一种预防方法，但是仍然有办法避免这种保护，例如使用程序，如 Frida，允许注入 snipes，允许您绕过这一措施。另一个例子是对应用程序执行逆向工程，改变散列(在 Android 上)并重新构建它。建议在应用程序与其后端之间的通信中对敏感信息使用加密算法，这样如果有人看到请求，请求就不会是明文。

# 临时演员

在 iOS 上安装 burp 证书
[在 iOS 设备上安装 Burp 的 CA 证书](https://portswigger.net/support/installing-burp-suites-ca-certificate-in-an-ios-device)

在 Android 上安装 burp 证书
[在 Android 设备上安装 Burp 的 CA 证书](https://portswigger.net/support/installing-burp-suites-ca-certificate-in-an-android-device)

Bash 代码，用于提取公共证书的 *sha256* 中的哈希(提取自: [Android Security: SSL Pinning。在 Android 应用中使用 SSL 很容易……|作者马修·多兰](/@appmattus/android-security-ssl-pinning-1db8acb6621e)

```
#!/bin/bash
certs=`openssl s_client -servername $1 -host $1 -port 443 -showcerts </dev/null 2>/dev/null | sed -n ‘/Certificate chain/,/Server certificate/p’`
rest=$certs
while [[ “$rest” =~ ‘ — — -BEGIN CERTIFICATE — — -’ ]]
do
 cert=”${rest%% — — -END CERTIFICATE — — -*} — — -END CERTIFICATE — — -”
 rest=${rest#* — — -END CERTIFICATE — — -}
 echo `echo “$cert” | grep ‘s:’ | sed ‘s/.*s:\(.*\)/\1/’`
 echo “$cert” | openssl x509 -pubkey -noout |
 openssl rsa -pubin -outform der 2>/dev/null |
 openssl dgst -sha256 -binary | openssl enc -base64
Done
```

# 参考

1.  [证书和公钥锁定控制](https://owasp.org/www-community/controls/Certificate_and_Public_Key_Pinning)
2.  [安装 Burp 的 CA 证书](https://portswigger.net/burp/documentation/desktop/tools/proxy/options/installing-ca-certificate)
3.  [安卓安全:SSL 牵制。在 Android 应用中使用 SSL 很容易……|作者 Matthew Dolan](/@appmattus/android-security-ssl-pinning-1db8acb6621e)