# SSL 固定的实现

> 原文：<https://medium.com/walmartglobaltech/implementation-of-ssl-pinning-7e57e280cc49?source=collection_archive---------7----------------------->

在我们的上一篇博客 [Mobile HTTP SSL PINNING:解码未知](/walmartglobaltech/http-ssl-pinning-decoding-the-untold-1fa751c63c3e)中，我们了解了 SSL PINNING 是什么？现在，在这篇博客中，我们将讨论实现 SSL 固定的可能方法，以及每种方法的优缺点。

![](img/33d7cca71510d7fd0a5fed4866c7ec23.png)

Image Source [https://pixabay.com/photos/implement-do-implementation-project-2372179/](https://pixabay.com/photos/implement-do-implementation-project-2372179/)

SSL 固定就是将证书相关信息存储在移动应用程序中(APK 或 IPA 文件)。有 4 种不同的方法可以锁定与证书相关的信息

*   *证书本身作为一个文件*
*   *证书哈希*
*   *证书的公钥。*
*   *公钥的哈希*

打算实现这一点的开发人员只剩下以上 4 种选择。因此，开发人员有必要了解哪一个更好，为什么？用推理。对于一个开发者来说，他需要理解证书的主要内容？。

让我们看看证书的主要内容有哪些:

*   域名
*   证书有效期
*   证书颁发机构(CA)详细信息
*   公开密钥
*   公钥算法
*   证书签名算法
*   SSL/TLS 版本
*   个性特征
*   指纹算法

通常，所有公司都有在特定时间框架后由根 CA 续订证书的策略。这一时间框架因公司而异，根据当今的标准，最长有效期为 825 天。因此，随着这种变化，证书的“有效期”也发生了变化。因此，证书内容将会改变，这进一步暗示了散列的改变。但是，公钥更改不会像证书更新那样频繁。因此，在移动应用程序中使用公钥是一个好主意。如果我们更进一步，公钥的散列更有意义。因为它需要较少的空间并且是一致的。

任何这种甚至适用于公钥锁定的锁定技术都有一个缺点。也就是说，当公钥轮换发生时，应用程序需要被强制更新。这本身就是一个问题。现在有办法解决这个问题吗？答案是肯定的，它被称为证书透明。

让我们稍微挖掘一下证书透明性，了解一下它是否比 SSL pinning 更好。

![](img/ae5d8a8989775abdf6f3d010a4d286c8.png)

Image source [https://certificate.transparency.dev/](https://certificate.transparency.dev/)

证书透明性与 SSL 固定的目的相同，但方式不同。在这种方法中，当 SSL 证书被颁发给移动应用程序时，它使用已经具有由可信根机构颁发的有效证书的副本的日志服务器来验证它是否有效。因此，如果黑客正在执行 MITM，他的根 CA 将不会出现在日志服务器中，用户将免受 MITM 攻击。

有关证书透明度的更多信息，请参考 [***链接***](https://www.certificate-transparency.org/) ***。***

起初，证书透明性似乎比 SSL 固定更好，但是理解其局限性/缺点也同样重要。让我们考虑一个场景，其中日志服务器关闭。在这里，即使对应用程序的有效调用也会失败，因为无法重新确认证书的有效性。这是这种方法的主要缺点。因此，不允许停机的高可用性应用程序，他们最好使用 SSL 固定，在那里他们有完全的控制权，而不是依赖于日志服务器。反之亦然，如果不关心高可用性，那么证书透明性比 SSL 固定更好。

最后，我们已经了解了固定和固定与证书透明性的最佳方式。在接下来的博客中，我们将讨论如何在 Android 和 iPhone 中实现 SSL pin。在那之前，注意安全。