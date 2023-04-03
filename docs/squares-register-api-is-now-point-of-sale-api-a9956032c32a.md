# Square 的注册 API 现在是销售点 API

> 原文：<https://medium.com/square-corner-blog/squares-register-api-is-now-point-of-sale-api-a9956032c32a?source=collection_archive---------0----------------------->

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

我们最近宣布了 Square Register 应用程序的新名称，以更好地反映它可以帮助您运营业务的一切: [Square 销售点](https://squareup.com/pos)。您可以使用该应用程序进行支付、跟踪销售、管理客户和员工等，从一个完整的销售点获得您需要的一切。

作为这一更广泛变化的一部分，我们还将注册 API 重命名为销售点 API。Square Point of Sale API 使用应用程序间通信，让您的 Android 或 iOS 应用程序打开 Square Point of Sale 应用程序，使用 Square 硬件(包括 [Square 非接触式和芯片读卡器](https://squareup.com/contactless-chip-reader))处理当面支付。

# 这是什么意思？

您将看到这一名称的变化反映在我们的文档和其他开发工具中:

*   我们的[开发者网站上的文档](https://docs.connect.squareup.com/)
*   针对 [Android](https://github.com/square/point-of-sale-android-sdk) 和 [iOS](https://github.com/square/SquarePointOfSaleSDK-iOS) 的开源 SDK
*   Maven、CocoaPods 和 Carthage 项目

为了确保您始终拥有我们开源 SDK 的最新版本，只需更新您的构建依赖项并更新您的代码，如下所述。

# Android 更新

在 Android 上，我们更新了框架名称和 Github 库，还创建了一个新的 Maven 工件。API 本身已经更新到 2.0 版本，在 Square Point of Sale 4.64 或更高版本中受支持(只需更新应用程序以确保您处于最新版本)。

如果您想要升级您的项目以利用将来的版本(除了名称更改之外，2.0 版本本身没有新的特性)，请遵循以下部分中的说明。升级后，您应该按如下方式更新代码，以反映名称的更改:

*   用`com.squareup.sdk.pos`替换对`com.squareup.sdk.register`包的引用
*   用`PosClient`替换对`RegisterClient`接口的引用
*   用`PosApi`替换对`RegisterApi`类的引用
*   用`PosSdk`替换对`RegisterSdk`类的引用
*   用`isPointOfSaleInstalled()`替换对`isRegisterInstalled()`方法的引用
*   用`launchPointOfSale()`替换对`launchRegister()`方法的引用
*   用`openPointOfSalePlayStoreListing()`替换对`openRegisterPlayStoreListing()`方法的引用

如果您使用 Web API 从您的网站启动 Square Point of Sale 交易，您可以更改您的 Web 意向键，将`com.squareup.register`替换为`com.squareup.pos`(请注意，这不是强制性的，因为 Point of Sale 应用程序将保持向后兼容)。

## 格拉德勒

要更新 SDK，请将`com.squareup.sdk:register-sdk:1.2`替换为`com.squareup.sdk:point-of-sale-sdk:2.0`,作为您的 build.gradle 中的依赖项:

```
dependencies { compile 'com.squareup.sdk:point-of-sale-sdk:2.0'}
```

## 专家

要更新 SDK，请将`com.squareup.sdk.register-sdk`1.2 版替换为`com.squareup.sdk.point-of-sale-sdk`2.0 版，作为 pom.xml 中的依赖项:

```
<dependency> <groupId>com.squareup.sdk</groupId> <artifactId>point-of-sale-sdk</artifactId> <version>2.0</version> <type>aar</type></dependency>
```

# iOS 更新

在 iOS 上，我们更新了框架名和 Github 库。我们还创建了一个新的 CocoaPod，并取消了原来的 Pod。

如果你在本地应用中使用我们的 SDK，你需要更新对 SDK 的引用。请注意，方法签名没有改变，因此除了更新导入声明之外，您的代码不需要任何更改。如果您使用销售点 API 构建了一个 web 应用程序，或者如果您的本机应用程序使用`square-commerce-v1://` URL 方案构建了自己的请求，则不需要进行任何更改来支持重命名。

为了升级您的项目，请遵循以下说明。

## 椰子足类

*   通过用`SquarePointOfSaleSDK`替换任何`SquareRegisterSDK` 条目来更新您的应用程序的 Podfile:

```
platform :ios, '9.0'target 'MyApp' do pod 'SquarePointOfSaleSDK'end
```

*   运行:`pod install —-repo-update`
*   将进口申报更新为`import SquarePointOfSaleSDK` (Swift)或`@import SquarePointOfSaleSDK` (Objective-C)

## 迦太基

*   通过用`github “Square/SquarePointOfSaleSDK-iOS”`替换任何`github “Square/SquareRegisterSDK-iOS”` 条目来更新你的应用程序的 Cartfile
*   运行:`carthage update SquarePointOfSaleSDK --platform --iOS`
*   将`$(SRCROOT)/Carthage/Build/iOS/SquarePointOfSaleSDK.framework`拖放到 General - >链接框架和库下的所有适用目标上；删除任何`SquareRegisterSDK.framework`条目
*   更新任何构建脚本输入文件路径，例如`$(SRCROOT)/Carthage/Build/iOS/SquarePointOfSaleSDK.framework`
*   更新进口申报以进口`SquarePointOfSaleSDK` (Swift)或`@import SquarePointOfSaleSDK` (Objective-C)

## Git 子模块

这些说明仅适用于之前已经将 SquareRegisterSDK-iOS 存储库添加为 Git 子模块的项目。否则，按照这些指令添加 SDK 作为子模块。

**注意:** Github 将所有 SSL 和 HTTPS 请求从旧 URL 转发到新 URL。远程存储库 URL**不需要**更新。

*   获取最新版本:

```
cd <SquareRegisterSDK-iOS submodule directory>git checkout master && git pull
```

*   这将把 SDK 的 XCode 项目名称更新为`SquarePointOfSaleSDK.xcodeproj`
*   在 XCode 中打开应用程序的项目或工作空间
*   在 XCode 文件浏览器中将`SquareRegisterSDK.xcodeproj` 替换为`SquarePointOfSaleSDK.xcodeproj`
*   在任何目标构建依赖关系中用`SquarePointOfSaleSDK.framework` 替换`SquareRegisterSDK.framework`

# 了解更多信息

你可以在[文档](https://docs.connect.squareup.com/)中了解更多关于 Square APIs 的信息。如果您有任何问题，请随时联系我们！