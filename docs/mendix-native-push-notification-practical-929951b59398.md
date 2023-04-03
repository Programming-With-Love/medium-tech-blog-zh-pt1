# Mendix 本地推送通知实用

> 原文：<https://medium.com/mendix/mendix-native-push-notification-practical-929951b59398?source=collection_archive---------0----------------------->

![](img/a6c636389c01089f63e956bb1bb093d8.png)![](img/b72a9986b7c3926df2a3029b6f975936.png)

使用 firebase 为 ios 和 android 推送通知，这比本地通知更复杂。我正在使用 Mendix 版，并使用本机快速入门模板创建一个新项目。首先，我们需要在 Mendix Appstore 中安装 2 个模块。

*   社区共享
*   加密

![](img/761bdd3538664580cf4b309d6aa05cec.png)

most of the app store modules will save under this folder, under the project

我们需要配置加密密钥常量。该值需要包含 16 个字符。

![](img/e5a2474fb1f0108fa8003206666cf3d5.png)

需要添加的重要模块是“推送通知连接器”

![](img/59ab71af16b8ea198a6d526da143e2fe.png)

**让我们开始对原生模板中的推送通知模块进行一些配置。**

*在 Home_Native 添加 app 事件*

![](img/f1a43a7c683ad335b368ebee374ac806.png)

为“加载”和“恢复”调用相同的微流

![](img/ff9a77c0996071aaacdfa1ec4c92d1ff.png)![](img/cec9571dad7a1c3520361eb4be496138.png)

创建一个新实体，将其命名为“NativeNotification ”,并使用 String(200)创建一个属性“objectGUID”。

![](img/e1957d11feeb3d9e43abc1822e83961e.png)

创建一个 nanoflow 来创建一个 NativeNotification 对象，并在最后返回它。

![](img/18f2f65879f68e96068330ed0c1e93e2.png)

在 Home_Native 页面上，将“Data View”小部件添加到页面中，将数据源设置为 nanoflow，并使用上面的 nanoflow。

![](img/06918acf1ef23a35186baf25b4dea616.png)

在数据视图中添加“通知”小部件。只需右键单击橙色高亮矩形，然后选择添加小部件。您可以输入搜索以快速查找。

![](img/b03014b2b213934ac28850947664e5f5.png)![](img/a26450de3901a5a462a1cc2ccbc2c9cf.png)![](img/739517225a60d76ec044ff24592713c2.png)

现在，双击通知。为 GUID 选择一个属性。输入动作名称为“推送”。然后我们将回头定义如何处理这个动作

![](img/0b87cf8910ddb848c3a1e2b8ab74b159.png)![](img/4e1ad23416496102eb37f614ed63bb8b.png)

我们转到导航，并在 web responsive 中添加一个新的菜单项。

![](img/9ff4b363eadc4c82580a57a74ac8db9a.png)

应该同步所有未使用的实体，在本机配置文件中下载所有对象

![](img/784460c7a1a99578852fcc4cd7d71f87.png)

创建 2 个纳流，ACT_OnReceive 和 ACT_OnOpen。每个操作都会创建不同的日志信息。

![](img/e836f5a95a2c244ce167daee3dd566ea.png)![](img/a1daf52fe29653b20918c3f9acd9cfbe.png)

现在回到 Home_Native，双击“Notification”小部件，用上面的这两个纳流映射动作。无论如何，您需要记住通知操作的名称。我们从一开始就把它命名为“推”。

![](img/34e6d31719e74ab77ba2921b99c3bb33.png)

运行本地和开放 web 响应配置 firebase。

打开 Firebase 控制台创建一个新项目，不要选择分析，因为这将花费一些预算。

打开推送通知管理页面，在 FCM 部分创建一个新的 FCM 项目。

![](img/aa7eb349d10784ea2c45417cc4b11e42.png)

要找到项目 id，请转到 firebase 控制台并从该菜单中获取它，然后

![](img/f7e2ae4fb5710974beafc7f0c95ffe05.png)

关键 JSON 怎么样。您可以从项目中生成它。

![](img/a34cbc5c2d14b13d70f77cb8412c7429.png)

毕竟还是省省吧。

对于 Android 和 IOS，请选择此项

![](img/dc34faa6a0af1878376b656f4ade2530.png)

现在，您需要创建 Github 个人访问令牌和应用中心令牌 API 密钥。这部分由于安全原因，我不能展示图片。你可以谷歌一下如何做到这一点。

***查看 Mendix 本地构建器参考文档，并按照说明***

 [## Native Builder — Studio Pro 8 指南| Mendix 文档

### 请更新到 Native Builder 3 . 2 . 1 版。Native Builder 3 . 2 . 1 版包括解决 GitHub 的…

docs.mendix.com](https://docs.mendix.com/refguide/native-builder) 

***在本站获取 nativebuilder.exe 文件***

[](https://www.dropbox.com/sh/hpw7sshut9bco68/AABackrr75rPSgW7u5LBMkMra?dl=0) [## 本地构建者

### Dropbox 是一项免费服务，可以让你将照片、文档和视频带到任何地方，并轻松分享。从不发电子邮件…

www.dropbox.com](https://www.dropbox.com/sh/hpw7sshut9bco68/AABackrr75rPSgW7u5LBMkMra?dl=0) 

命令应该是这样的。注意图标应该在 ***1024x1024、*** 处，闪屏应该在 ***1440x2560*** 、**处，当你看到——，这表示 2 个破折号。**

## 将包准备到 GitHub 和应用中心的脚本。

*native-builder.exe prepare—github-access-token yourkeyhere—app center-API-token yourkeyhere—Java-home " C:\ Program Files \ Java \ JDK-11 . 0 . 8 "-MX build-path " C:\ Program Files \ Mendix \ 8 . 14 . 0 . 6148 \ modeler \ MX build . exe "-project-path " E:\ Mendix \ testtraining day 4-main \ testtraining day 4 . MPR "-project name test day 4—app-identifier "*

如果一切正常，您将获得以下结果

![](img/6aef3869ee7c85d47d2ed1fb6b2e18cd.png)

## ios 的配置授权

*native-builder.exe 配置 ios 添加-授权-项目名称测试日 4-授权通知*

![](img/24b840c963a24d7d325b9d347b803290.png)

## ios 的配置背景模式

*native-builder.exe 配置 ios 添加-背景-模式-项目名称测试日 4-模式通知*

![](img/2980e9ac137d3269b749f211a6cc2874.png)

## 现在，我们可以为测试构建应用程序了

*native-builder.exe 版本—项目名称测试日 4 —应用程序版本“1 . 0 . 0”—版本号 1*

这需要一些时间来完成，我想大约 5-10 分钟。这个以后应该会改进，开发者可以在 studio pro 上搭建。

![](img/14c5128279181287ea22da070fd9add0.png)

如果成功，那么您将会在与本地构建器文件夹相同的目录中找到文件夹构建

![](img/462395520cda1e8bb052c4f435a1b31d.png)![](img/4e571d99ba2693463dcdee7f329c88f3.png)

或者，您可以到应用程序中心进行构建，这需要几分钟时间，完成后，您可以看到下载按钮。

![](img/471d06cab38f19a49bac5e506dbf6454.png)

构建完成后，您可以下载 apk 文件。并提交回 Github

![](img/4be5f64d1cc30d118fec20b223ec3d8d.png)![](img/cc541711c6845503e3ed36463914df0b.png)

在使用 Firebase 的推送通知中，我们需要考虑一个技术架构，我们需要服务器通过 SSL 握手与服务器进行通信。因此，您不能通过在本地运行 Mendix 来发送消息。如果你这样做，它会给你一个错误 403。

![](img/4e571d99ba2693463dcdee7f329c88f3.png)

我们可以将 Mendix 部署为免费云。但是，我想把 Mendix 云节点作为一个许可节点来介绍。

在您的组织帐户下，选择节点。如果您使用的是企业许可证，应该有节点菜单。

![](img/8a70c0fe2f9d13f65b527fe5189ce4c1.png)

让节点连接到您的 Mendix 应用程序。我想分享一个重要的注意事项，你的应用程序应该在节点能够像这样识别之前断开与免费云的链接

![](img/6b316600e9e59afd355314ba9453f815.png)

## 如何解除你的应用的链接，并将其从免费云中释放。

在你的开发者账户下进入 Mendix home 的“我的应用”。在“环境”菜单中，您可以在顶部看到“取消应用链接”。

![](img/8f5b76c08d825fce24fa5baa5f1a0166.png)

点击这个后，弹出窗口会显示出来，然后按我高亮显示的按钮。您将看到一个持续弹出窗口，要求进行身份验证。

![](img/d448e40e72a9be5b9e018ef87706bafc.png)

然后你按下“发送短信”。它将发送到您添加到开发人员个人资料中的手机上。

![](img/2566aac0b78892a58632bfb8bb0e6b29.png)

您将看到弹出窗口进行验证。

![](img/1a9dcb50435cea5b64192c96db2afe9a.png)

它会一直要求确认

![](img/cc511e3302aae886d3ab7b53809820e6.png)

然后再确认一次

![](img/d448e40e72a9be5b9e018ef87706bafc.png)

大家都会认为这一步就完成了。然而，这还没有结束。您需要再次单击“取消链接”。

![](img/0ce13424974240e6c29102871906f4b3.png)

当你点击这个弹出窗口后，你的免费应用就会被解除链接

![](img/8d739e92f87f3b09ee25286443e52e18.png)

现在您可以选择节点

![](img/61426a937ae34f9f56aac160790d60be.png)

## 选择节点后。

当您的应用程序成功连接到节点时，您的屏幕将如下所示

![](img/f90c172eb7f6e15482e2dc079801b217.png)

为了进行部署，我们需要像往常一样创建一个新的包。然后点击部署。这将是验收比生产的顺序。我的节点只是一个简单的，对于大的，你可能在验收环境之前有一个测试环境。

![](img/46dc62965009236e73f13ebd0ee0f610.png)

让我们看一下验收环境的细节。在 Model Options 选项卡上，可以看到我们刚刚在 Mendix studio pro 上配置的所有信息都将出现在该屏幕上。

![](img/eb0496798bd62b3e5abac68ecbbb2e24.png)

## 本机构建器注意事项

***每次在 Mendix Studio Pro 中进行更改并重新部署时，都需要再次运行 nativebuilder.exe 准备和构建。否则，它不会反映你做了什么。***

现在，您可以测试推送通知 Android 应用程序了。

![](img/e4ed8aa46d54bfed0d5dab76691df66d.png)

输入信息，然后发送。

您将在下面的视频中看到类似这样的消息。

## iPhone 怎么样。

iPhone 更多的是关于安全性，这是它的本性。我们需要一个签名密钥来创建一个. ipa 文件。

签名构建需要 2 个文件

*   ***配置文件。移动预配***
*   ***证书. p12***

***我们需要一个苹果开发者账号和安装了 Xcode 12 的 MacBook。***

首先，我们需要使用允许推送通知的 app id 选项创建一个标识符。按照下面的步骤

***苹果开发者账户上的标识符***

![](img/e0d750a1557f1f65f51dfa217194613a.png)![](img/337862de1d2025e6dca029b711001121.png)![](img/1c259921aa8b1fbfe9d05c7ad6fefaa3.png)![](img/be857530b2a32420ec171c49fb94d2dc.png)![](img/26a1eeb1b629e125d8e9541a8ad73085.png)

***使新配置文件包含标识符、证书和设备***

![](img/6e20e7dc205c9778d8361d0a9244aa79.png)![](img/ba37549bd3f71a1f7c96d3b2cc410b41.png)![](img/faeae82a02e906fd0505fe798a1aedc9.png)![](img/59175d07a6680f7642c39bb1e2b24bbb.png)![](img/cba82a5ad446014fdfbc617ed85731d5.png)![](img/9e8c52b82eb1cdaee298e569278b0569.png)![](img/469d346a79c2d9fbe61c60c1ba6478bb.png)![](img/2d820f49faa17e6ac530c75b1f0e0d72.png)

***通过 macOS 钥匙扣*** 导出开发者证书

![](img/1fb1a605a1191e459282bc3eb2231cf0.png)![](img/82da5e8c10706d6b4872f46423cfb1b9.png)![](img/ae7b04efcdfd64b96d284ee7ea911180.png)

***最后，我们还有 2 档***

![](img/46b7c563e4befe157af1f70f83e783c3.png)

***然后在构建配置下添加应用内中心，然后按“保存并构建”。***

![](img/996fb78ae64eb128068074fe63635bf0.png)

***如何手动建立***

复制 git hub 源代码路径，并在本地克隆它。

![](img/1cd40c6891ebfa4487f05dce439bbab6.png)

如果您的计算机上没有安装 pod，请安装它

![](img/4882b8d363bd17dade26a283361e4c66.png)

用 VSCode 打开您的项目

运行此命令

![](img/d09f15c01e78910af60c607b8adf65f9.png)

然后 cd 到 ios

运行此命令

![](img/09e7b9edfad3f3609d824fe30422ba32.png)

哎呀，有人会像我一样得到这个错误

![](img/0390ee9cac76a1835aaba5aceb30c65c.png)

为什么？因为路径不是指向 Xcode 的点。

要解决这个问题，你必须这样做

![](img/2709221a95bf844afdc04d94b114df11.png)

然后再次运行下面的命令

![](img/8db1879e3304a794928f0f70cce90f42.png)

然后再次运行 pod 安装

![](img/62dd0abb1abccad24813814f82404ac1.png)

现在使用 Xcode 打开所有 ios 文件夹

然后选择签名

![](img/a14f7f5c8f3fe38c3d8204ecfbbf6eec.png)

然后只需按下按钮建立

![](img/5301d9e65b6b2d83ad7c7b3a319ece7c.png)

现在，您可以返回 web 后端来推送测试消息。

![](img/07d2ae8408c82d4a67ee0ffeeb507bca.png)![](img/81bb391d9a39c7b900966babdbeed6af.png)

哎呀，您出错了。

![](img/6437a63fe830da8cf07f8a688ecf4485.png)

对此的解释是。为了让 FCM 向 IOS 推送通知，我们需要 APNS。这是一项关键服务，我们需要在 Apple 开发者帐户中创建它。获得密钥下载后，您可以添加到 ios 应用程序的“云消息”下的 firebase 项目中

我们开始吧

![](img/3267df65881256061a5eba9028e979f7.png)![](img/e1797eab7200b02576fd910a9c8c6ed9.png)![](img/ed044fc34f8e3fc50d46d997a379f05d.png)

文件格式应该是这样的

![](img/8d0aa42bf202334ae7d6ab6f4ce7233e.png)

回到 firebase 控制台，将. p8 上传到 firebase 控制台。定义您的. p8 key id 和 Apple 开发团队 id(在您的个人资料下)。如果不能立即添加，只需刷新并再次回来添加即可。

![](img/a6c5e40faa028ac589b84cba119f77d5.png)![](img/03c8f8b93e61e8e596a6fb1916a7ab0b.png)

我们再推一次。

![](img/81bb391d9a39c7b900966babdbeed6af.png)

结果可能会像下面这个视频。

仅此而已。

下次在 google play 和 apple store 上构建时再见。