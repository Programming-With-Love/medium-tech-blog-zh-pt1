# 发布私人应用变得更加容易

> 原文：<https://medium.com/androiddevelopers/publishing-private-apps-just-got-easier-40399c424b8a?source=collection_archive---------4----------------------->

![](img/9dfcd6e8d26b7f626a57b18aefe0a8b7.png)

Illustration by [Virginia Poltrack](https://twitter.com/VPoltrack)

无论您的组织有 5 个还是 100 个应用程序，都有工具可以帮助您自动管理所有 Play Store 列表。Google Play 有一个开发者 API，可以管理 Play 商店列表、apk 等等。2017 年 1 月，谷歌从 Twitter 收购了开发者工具套件 [Fabric](http://fabric.io/blog/fabric-joins-google/) ，作为此次收购的一部分是应用自动化工具套件 [*fastlane*](https://fastlane.tools/) 。 *fastlane* 可以自动截图、管理测试版部署，以及将应用程序签名/推送至 Play store。

此外，[自定义应用发布 API](https://developers.google.com/android/work/play/custom-app-api/get-started) 支持受管 Google Play 用户创建私有托管应用，无需[最低版本检查](https://developer.android.com/distribute/best-practices/develop/target-sdk)。[托管 Google Play](https://support.google.com/googleplay/work/answer/6137711?hl=en) 是一个面向 Android 企业的市场，增加了对私有应用的支持。[私有应用](https://support.google.com/a/answer/2494992?hl=en)是只分发给内部用户，不对外公开的 Android 应用。创建后几分钟内即可部署私有应用。一个*快速通道* [拉请求](https://github.com/fastlane/fastlane/pull/13421)，由[Jan piotrovski](https://github.com/janpio)构建，一个核心贡献者，被构建来允许一个无代码的部署方法。关于功能请求的历史记录在本期 github 中[这里](https://github.com/fastlane/fastlane/issues/13122)。有关托管 Google Play 和 Google Play Protect 的更多背景信息，请参见这篇[博文](https://www.blog.google/products/android-enterprise/safely-and-quickly-distribute-private-enterprise-apps-google-play/)。

**为什么重要:**自定义应用发布 API 或 *fastlane* 极大地简化和减少了迁移到托管 Google Play 的摩擦，并集成到持续集成工具和流程中。

# 设置

**重要提示:**在创建调试和生产密钥库时，确保使用以下用于[应用程序签名的最佳实践](https://developer.android.com/studio/publish/app-signing)。不要丢失您的生产密钥库！一旦在 Google Play 上与应用程序 id 一起使用(包括私有应用程序)，如果不创建新的应用程序列表并修改应用程序 id，就无法更改密钥库。

**推荐:**利用 [Google Play 应用签名](https://developer.android.com/studio/publish/app-signing#google-play-app-signing)为您的 apk 签名。这是一个安全的选项，可以确保您的密钥库不会丢失。请看这里的实施细节[。](https://support.google.com/googleplay/android-developer/answer/7384423?hl=en)

**重要提示:**Google Play 上的所有应用(包括私人应用)都必须有唯一的应用 id，不能重复使用。

发布私人应用程序时，您需要执行 3 个步骤才能发布。

请遵循[设置说明](https://developers.google.com/android/work/play/custom-app-api/get-started)，该说明将指导您完成以下步骤:

1.  在云 API 控制台中启用 Google Play 自定义应用发布 API
2.  创建一个服务帐户，下载一个 JSON 格式的新私钥。
3.  启用私人应用程序，遵循说明。

# 快速通道设置

*   请参见此[文档](https://docs.fastlane.tools/getting-started/android/setup/)安装*快速通道。*fast lane 包含托管 google play 支持。

# 启用私有应用程序-获取开发者帐户 Id

本[指南](https://developers.google.com/android/work/play/custom-app-api/get-started)展示了创建私有应用程序的步骤，这需要创建一个 OAuth 回调来接收 developerAccount id。有两种方法可以启用私有应用程序:使用 fastlane 或使用 API。以下是如何使用每个选项及其难度:

## 使用快车道——简单

```
> fastlane run get_managed_play_store_publishing_rights
```

**示例输出:**

```
[13:20:46]: To obtain publishing rights for custom apps on Managed Play Store, open the following URL and log in:[13:20:46]: [https://play.google.com/apps/publish/delegatePrivateApp?service_account=***SERVICE-ACCOUNT-EMAIL***.iam.gserviceaccount.com&continueUrl=https://fastlane.github.io/managed_google_play-callback/callback.html](https://play.google.com/apps/publish/delegatePrivateApp?service_account=SERVICE-ACCOUNT-EMAIL.iam.gserviceaccount.com&continueUrl=https://fastlane.github.io/managed_google_play-callback/callback.html)[13:20:46]: ([Cmd/Ctrl] + [Left click] lets you open this URL in many consoles/terminals/shells)[13:20:46]: After successful login you will be redirected to a page which outputs some information that is required for usage of the `create_app_on_managed_play_store` action.
```

将链接粘贴到 web 浏览器中，并向您的帐户所有者验证托管 play 帐户，将会发送

## 使用 API —中等

**如果**您不打算构建一个 web 用户界面来管理您的应用程序，您可以使用下面的这个基本节点脚本并启动 Firebase 函数来快速轻松地获得 developerAccountId。如果您不介意，您可以将 continueUrl 设置为 [https://foo.bar](https://foo.bar) (或另一个假 Url)来获取 developerAccountId，尽管出于安全目的不建议这样做。

**用于 Firebase 设置的云函数**

本[指南](https://firebase.google.com/docs/functions/get-started)展示了如何设置云功能。以下代码可用于端点。

functions/index.js

# 创建私人应用列表

## 使用快车道——简单

Example Fastfile

```
> fastlane create_private_app
```

## 使用 API —中等

API [文档](https://developers.google.com/android/work/play/custom-app-api/publish)。客户端库在 [Java](https://developers.google.com/api-client-library/java/apis/playcustomapp/v1) 、 [Python](https://developers.google.com/api-client-library/python/apis/playcustomapp/v1) 、 [C#](https://developers.google.com/api-client-library/dotnet/apis/playcustomapp/v1) 和 [Ruby](https://developers.google.com/api-client-library/ruby/apis/playcustomapp/v1) 中可用。

## API 示例

用 Ruby 编写的这个示例代码使用一个 [Google 服务帐户](https://developers.google.com/android/work/play/custom-app-api/get-started#create_a_service_account) json keyfile 进行身份验证，然后调用 Play 自定义应用服务来创建和上传第一个版本的私有 APK。此代码仅在首次创建应用程序时使用，后续更新应使用 Play Publishing API 中的 upload apk 功能。

# 更新私人应用程序

一旦创建了一个私人应用， [Google Play 发布 API](https://developers.google.com/android-publisher/) 可以在 Play store 列表的初始创建之后推送新的 apk。 *fastlane* 支持这个功能上传新的 apk 来玩，更多信息可以在[这里](https://docs.fastlane.tools/getting-started/android/release-deployment/)找到。

# 部署给用户

托管 Google Play 需要一个 EMM(企业移动管理)系统来向用户分发应用。更多信息[此处](https://support.google.com/googleplay/work/answer/6145139?hl=en)。

部署和管理您的私有企业应用从未如此简单。通过托管 Google Play 部署应用程序的两种方法都是可行的，这都取决于你的 CI 系统以及你是否想编写任何代码。给[快车道](https://fastlane.tools)打个预防针，它会节省你大量的时间。

如果你遇到任何问题，可以在 [github](https://github.com/fastlane/fastlane/issues) 上对 fastlane 提出错误。