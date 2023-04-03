# 在 Mac 版 Office 中实现对等

> 原文：<https://medium.com/walmartglobaltech/reaching-parity-in-office-for-mac-1be84c81e341?source=collection_archive---------7----------------------->

![](img/fabf5e57224bfd6fe15475e6b5cd3f26.png)

Photo Credit: [SamuelFrancisJohnson](https://pixabay.com/en/meditation-stone-towers-stone-tower-2262835/)

作为正在进行的安全研究的一部分，我们试图使我们的 macOS 系统的安全性与我们的 Windows 系统保持一致，我们发现了几个 Office for Mac 没有与其 Windows 同胞相同的控制的地方。我们就这些漏洞联系了微软，他们为 Office for Mac 提供了两个额外的控件，以更好地保护 macOS 系统。

# VBAObjectModelIsTrusted

一个机会是在 Mac 版 office 中打开的 Office 文档能够动态生成 Visual Basic for Applications (VBA)模块。这在信任中心的 Windows 上被锁定，设置为“信任对 VBA 项目对象模型的访问”微软在这里描述了这个设置:

```
"Disallow or allow programmatic access to the Visual Basic for Applications (VBA) object model from an automation client. This security option is for code written to automate an Office program and manipulate the VBA environment and object model. It is a per-user and per-application setting, and denies access by default, hindering unauthorized programs from building harmful self-replicating code. For automation clients to access the VBA object model, the user running the code must grant access. To turn on access, select the check box."
```

以前，在 Office for Mac 中，这只能在 PowerPoint 中实现。微软将在 16.21 版 Office for Mac 2019 中的其他产品上部署该设置。将有一个配置首选项“VBAObjectModelIsTrusted ”,它将允许管理员控制 Office 套件是否允许访问 VBA 对象模型。默认情况下，对对象模型的访问将被阻止。在这两个操作系统之间，该控件的应用有一个微小的区别。在 Windows 上，这些控件是基于用户和应用程序的。在 macOS 上，这些控制是针对每个用户的，适用于整个微软产品套件。

# 尝试默认密码

在我们进入这一新配置之前，我将参考几篇文章:

*   [https://isc . sans . edu/forums/diary/Encrypted+Office+Documents/23774/](https://isc.sans.edu/forums/diary/Encrypted+Office+Documents/23774/)
*   [https://naked security . sophos . com/2013/04/11/password-excel-velvet-sweat shop/](https://nakedsecurity.sophos.com/2013/04/11/password-excel-velvet-sweatshop/)

好了，现在我们已经给出了这个用来自动解密 Excel 工作簿的古老默认密码的一些高级背景…我们可以讨论如何禁用它。在 Windows 上，可以将以下注册表项设置为 0 来禁用此功能:

`HKCU\Software\Microsoft\Office\1?.0\Excel\Security\TryDefaultPassword`

在 Office for Mac 上，没有匹配的配置来禁用此功能。截至 Office for Mac 版本 16.20(2018 年 12 月发布)，微软已经发布了配置首选项“TryDefaultPassword”。此配置首选项将默认为“是”，这意味着 Excel 将继续为您静默解密这些文件。为了保护您的系统，在测试之后，您应该将此配置更改为 NO

这两个首选项都与 CFPreferences 兼容，如果通过配置文件部署，则被视为计算机范围的策略。

# TL；速度三角形定位法(dead reckoning)

*   升级到 Office 365/2019 for Mac，并保持您的版本最新
*   将此配置偏好设置部署到您的 macOS 系统

```
defaults write com.microsoft.Excel TryDefaultPassword -bool NO
```

*   如果您在 windows 系统上，请将以下注册表项设置为 0

```
HKCU\Software\Microsoft\Office\1?.0\Excel\Security\TryDefaultPassword
```

# 参考

1.  [https://support . office . com/en-us/article/enable-or-disable-macros-in-office-files-12b 036 FD-d140-4e 74-b45e-16 fed 1 a 7 e 5c 6](https://support.office.com/en-us/article/enable-or-disable-macros-in-office-files-12b036fd-d140-4e74-b45e-16fed1a7e5c6)
2.  【https://macadmins.software/docs/VBSecurityControls.pdf 号

# 特别感谢

[埃里克·施韦伯特](https://twitter.com/schwieb) —微软
[保罗·鲍登](https://twitter.com/mrexchange) —微软
[丹尼·克里斯提](https://twitter.com/DisK0nn3cT) —沃尔玛[disk 0n 3 CT](https://medium.com/u/c43246a6a1ea?source=post_page-----1be84c81e341--------------------------------)