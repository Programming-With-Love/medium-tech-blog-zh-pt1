# 原生应用注定要失败

> 原文：<https://medium.com/javascript-scene/native-apps-are-doomed-ac397148a2c0?source=collection_archive---------0----------------------->

![](img/26b6212108bd321ffe99251f27c77a5f.png)

2014–09 Vulture — Dick Knight (CC BY-NC-ND 2.0)

从现在开始，我不会再开发任何原生应用。我所有的应用都将是渐进式的网络应用。渐进式网络应用程序是一种网络应用程序，旨在比原生移动应用程序更无缝地在移动设备上工作。

我说的“更无缝”是什么意思我的意思是，大多数网络流量来自移动设备，用户平均每月安装 0-3 个新应用。这意味着人们不会花很多时间在 app store 中寻找新的应用程序，但他们会花很多时间在网络上，在那里他们可能会发现并使用你的应用程序。

渐进式网络应用程序开始时就像任何其他网络应用程序一样，但当用户返回应用程序并通过使用证明他们有兴趣更经常地使用应用程序时，浏览器会邀请用户将应用程序安装到他们的主屏幕上。PWA 也可以受益于[推送通知](https://developers.google.com/web/fundamentals/engage-and-retain/push-notifications/)，就像本地应用一样。

这就是有趣的地方。就像任何原生应用程序一样，progressive web 应用程序将有自己的主屏幕图标，当你点击它时，应用程序将在没有浏览器 chrome 的情况下启动。这意味着没有地址栏和网页导航界面。只有手机通常的状态栏和你的应用程序几乎全屏显示。

这是一个漫长的过程。没有一项技术是特别新的——除了新兴的跨平台标准。

# 一些历史

iPhone 早期，没有 app store。史蒂夫·乔布斯希望开发者使用标准的网络技术开发 iPhone 应用。

有时候远见卓识者是正确的，但是他们比他们的时代超前 10 年。回顾两年前，史蒂夫·乔布斯为 iPhone 开发网络应用的建议被《福布斯》称为他的[【最大的错误】](http://www.forbes.com/sites/markrogowsky/2014/07/11/app-store-at-6-how-steve-jobs-biggest-blunder-became-one-of-apples-greatest-strengths/)，因为原生应用获得了巨大的成功。

今天回想起来，很明显他真的发现了一些东西——远远超出了当时现有网络标准的能力。

十年后，移动网络标准现在支持开发者在原生应用中寻找的许多功能，史蒂夫·乔布斯最初对移动网络应用的愿景现在正被世界其他地方认真追求。苹果几乎从一开始就支持“支持苹果移动网络”的网络应用，你可以使用元标签将这些应用添加到你的主屏幕上，元标签可以帮助 iOS 设备找到合适的图标等东西。

其他供应商也纷纷效仿，各自创建自己的元标记集合来声明移动 web 应用程序的功能，但最近，一个跨平台规范被引入，现在，跨平台移动 web 应用程序终于成为现实。

实现该标准的应用程序被称为渐进式网络应用程序，不要与渐进式增强或响应式应用程序等容易混淆的类似术语相混淆。

# 什么是渐进式网络应用？

渐进式网络应用程序是专为移动设备设计的网络应用程序。如果浏览器发现用户想要继续使用该应用程序，它可能会提示用户将它安装到他们的主屏幕、dock 等。但是，为了获得资格，他们必须满足特定的标准:

*   一定是 HTTPS(见[让我们加密](https://letsencrypt.org/))
*   具有所需属性的有效清单( [Web 清单验证器](https://manifest-validator.appspot.com/)
*   必须有服务人员
*   Manifest `start_url`必须始终加载，即使离线(使用服务人员)
*   必须提供自己的导航
*   应能适应不同的屏幕尺寸和方向

当然，为离线用户使用 HTTPS 和服务人员对于任何现代应用程序来说都是很好的实践。

许多应用程序开发者似乎忘记了一点，如果你构建一个渐进式网络应用程序，你必须能够在没有浏览器 chrome 和浏览器导航手势的情况下导航应用程序。移动设备假设你已经在应用程序中内置了自己的导航功能。

例如，如果您有一个“关于”页面，该页面必须有一个返回到应用程序 UI 的链接，否则用户必须关闭并重新打开应用程序才能返回到您的主应用程序 UI。

# 渐进式网络应用程序操作指南

有很多关于构建渐进式 web 应用程序的信息散布在整个 web 上，但是其中很多都已经过时了，而且很多都只包含了构建渐进式 web 应用程序所需的一小部分内容。让我们解决这个问题。

# 启用 HTTPS

**【编辑:2020 更新】:**使用 [Next.js](https://nextjs.org/) 并使用 [Vercel](https://vercel.com/) 进行部署，HTTPS 以及页面捆绑拆分、静态服务器渲染、CDN 交付、缓存头管理和 GitHub CI/CD 部署集成将自动开箱即用。这就像拥有世界上最好的全职 DevOps 团队，但他们不是每年花费数十万美元，而是每月为您节省数千美元的托管费用。

以下是以前的说明，这样我们就可以嘲笑我们曾经为了开发一个应用程序而不得不经历的所有困难:

> 要启用 HTTPS，您需要:
> 
> *网络服务器(我推荐[数字海洋](https://m.do.co/c/fdcfedac5208))
> 
> *一个 [SSL 证书](https://certbot.eff.org/all-instructions/#ubuntu-16-04-xenial-nginx)
> 
> *强大的迪菲-海尔曼集团(`sudo openssl dhparam -out /etc/ssl/certs/dhparam.pem 2048`)
> 
> web 服务器的 TLS/SSL 配置([Ubuntu 上 Nginx 的说明](https://www.digitalocean.com/community/tutorials/how-to-secure-nginx-with-let-s-encrypt-on-ubuntu-14-04))

# 清单

清单文件叫做`manifest.json`，它非常简单。它由名称(`short_name`表示主屏幕图标，可选的`name`表示更完整的名称)、开始 url 和一个大的图标列表组成，因此您可以支持各种平台所需的各种不同的图标大小。对于 Android + iOS，您需要:

*   36x36
*   48x48
*   60x60(苹果触摸图标 iPhone)
*   72x72
*   76x76(苹果触摸图标 iPad)
*   96x96
*   120x120(苹果触摸图标 iPhone retina)
*   152x152(苹果触摸图标 iPad retina)
*   180 x180(iOS 8+的 Apple touch 图标)
*   192x192
*   512x512

我挑出苹果触摸图标，因为它们有众所周知的名字:

`apple-touch-icon-180x180.png`

其中 180×180 可以由任何特定分辨率来代替。不要求使用众所周知的名称，但是如果您忘记包括标签，iOS 仍然可以通过在您的 web 应用程序的根目录中搜索图标来找到它们，如果您使用众所周知的名称的话。

**iOS 图标不支持透明。**

## **Sample manifest.json:**

有些功能你应该了解一下。`theme_color`设置在 Android 上切换应用程序时使用的状态栏和窗口标题栏的颜色。

`background_color`设置闪屏上使用的颜色。在 Android 上，一个闪屏将由`name`属性(长名称)和一个位于`background_color`顶部的大图标组成。

## **清单并不是随处可见的**

我第一次构建渐进式 web 应用程序时，我很高兴它在 Android 的 Chrome 上能像预期的那样工作，但当我在 Safari/iOS 中查看它时，它似乎不能工作。原因在于，尽管 mobile Safari 使用自定义标签支持这些功能已经有十年左右的时间了，但它还不支持 web 清单规范。

因此，除了支持的浏览器的清单文件之外，您还需要 iOS 的特殊元标记，从这个开始，它将启动没有浏览器 chrome 的应用程序:

```
<meta name="apple-mobile-web-app-capable" content="yes">
```

不过，有很多标签需要记住，而且可能有另一种方法。有一个 [web manifest polyfill](https://github.com/boyofgreen/manUp.js/) ，它将读取你的 manifest.json 文件，并为旧的移动浏览器、iOS，甚至 Windows phone 和 Firefox OS 添加特定于供应商的标签。

# 服务行业人员

Service Worker 是一个最新的 web 平台规范，允许您在本地缓存资源，以确保您的应用程序仍然工作，即使用户没有连接到互联网。

它的工作原理是劫持您的网络请求，并在用户离线时从本地缓存提供响应。然而，事情远不止如此。这是一个相当复杂的低级 API，允许你做很多事情来优化用户的体验，无论他们是在线还是离线。

首先，我将推荐一个非常简单的高级抽象。一个叫 [UpUp](https://www.talater.com/upup/) 的小脚本。使用 UpUp 真的很简单。这里有一个来自 UpUp 文档的例子:

`content-url`是用户离线时加载的 URL。我只是使用应用程序的根 URL，它工作得很好。

这些资产是需要在本地缓存的文件，以便应用程序正常运行。记住要确保所有的图片、图标、CSS，甚至默认的 AJAX 请求响应都包含在内。如你所见，任何文件类型都可以。

最终，您可能希望对离线资源缓存进行比 UpUp 提供的更多的控制。当那一天到来时，这里有一些很好的教育资源可以帮助你开始学习:

*   [免费的谷歌 Udacity 课程](https://www.udacity.com/course/offline-web-applications--ud899)
*   [服务人员食谱](https://serviceworke.rs/)进行深入报道。

# 测试

## 使用 Chrome inspect 调试物理设备

将您的设备插入 USB 电缆。在 Android 设备上打开 USB 调试。参见[远程调试说明](https://developers.google.com/web/tools/chrome-devtools/remote-debugging/?utm_source=dcc&utm_medium=redirect&utm_campaign=2016q3)。你可能需要谷歌一下，弄清楚如何将你的 Android 手机设置为开发者模式，以实现远程调试。

打开开发人员模式后，您可能会在设置模式中看到开发人员选项。打开它，并确保 USB 调试已打开。

拜访`chrome://inspect#devices`。点击 inspect 按钮，您将获得应用程序的完整开发工具。

# 验证您的服务人员

访问`chrome://inspect/#service-workers`以验证您的服务人员是否正常工作。

# 验证安装到主页

如果您想跳过用户参与度检查并总是获得安装到主页选项，请打开 Chrome flags 中的“跳过用户参与度检查”:

`chrome://flags/`

要在桌面上测试，您还应该翻转“启用添加到工具架”。

# **本地应用尚未消亡**

渐进式网络应用程序现在拥有原生应用程序的大部分功能，安装摩擦有望低于原生应用程序，你将不再需要担心应用程序商店的看门人，你也不必为进入应用程序商店的特权而向任何人支付 30%的应用程序销售税。

但是本地应用程序仍然有一些移动网络应用程序在很长一段时间内都不具备的功能。

值得注意的是，大多数传感器和硬件集成规范在大多数浏览器中受到[限制或不支持](https://www.w3.org/Mobile/mobile-web-app-state/#Sensors_and_hardware_integration)，甚至像设备定位 API 这样的基本功能也经历了突破性的变化，多个版本的规范存在于各种浏览器中，需要一些复杂的逻辑或聚合填充才能安全使用。

> **【编辑:2020】**苹果现在支持 service worker 和 web manifest，甚至苹果也在构建 PWAs，但他们只是称之为可安装的 web 应用。

# 结论

从短期来看，您可能仍然希望开发一个混合应用程序，它可以利用一些设备 API，而这些 API 在使用 web 标准时仍然是不可用的。

在构建了我的第一个渐进式 web 应用程序后，我对未来充满希望，但我也意识到它并不完美。让一切在所有设备平台上顺利运行确实需要一段时间。你还必须记住，你会错过应用商店中用户熟悉的发现功能和众所周知的安装程序。

希望浏览器厂商能跟上这一愿景，最终渐进式网络应用的安装体验会比本地应用好得多。看起来事情往那个方向发展了。

当然，原生应用还会存在一段时间，但如果你正忙于学习 Swift 或 Java 以便构建原生应用，你可能会考虑学习 JavaScript。

**【更新 2020】**想试试一些进步的 web apps？所有这些现在都有 pwa:

*   **Instagram**
*   **推特**
*   **优步**
*   **Spotify**
*   **星巴克**
*   **Pinterest**
*   **~80k 其他 app**

# 编辑:网络平台很棒

如果你想在评论中告诉我，用网络平台构建一个严肃的应用程序是不可能的:

现在是 2016 年，不是 2004 年。网络平台已经走过了漫长的道路。阅读[“十大必看的网络应用和游戏”](/javascript-scene/10-must-see-web-apps-games-36ab9ca60754)来看看其他人为网络平台做了些什么。

还不服气？阅读[“为什么原生应用注定要失败:原生应用注定要失败 pt 2”](/javascript-scene/why-native-apps-really-are-doomed-native-apps-are-doomed-pt-2-e035b43170e9)。

[2020]这篇文章已经过编辑，以反映 Apple 已经实现服务工人和标准 web 清单文件的事实。

[2020]本文已更新，以反映当今世界上许多最受欢迎的应用程序都有 pwa 这一事实。这比我预期的要快！

想要构建跨平台的通用 JavaScript 应用程序？

# [跟随 Eric Elliott 学习 JavaScript】](http://ericelliottjs.com/product/lifetime-access-pass/)

***埃里克·艾略特*** *著有* [*【编程 JavaScript 应用】*](http://pjabook.com) *(奥赖利)，以及* [*【跟埃里克·艾略特学 JavaScript】*](http://ericelliottjs.com/product/lifetime-access-pass/)*。他为 Adobe Systems******Zumba Fitness*******华尔街日报*******ESPN******BBC***等顶级录音师贡献了软件经验******

**他大部分时间都在旧金山湾区和世界上最美丽的女人在一起。**