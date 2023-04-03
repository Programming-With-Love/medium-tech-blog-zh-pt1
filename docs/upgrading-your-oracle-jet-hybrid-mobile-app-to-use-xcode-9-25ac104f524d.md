# 升级您的 Oracle JET 混合移动应用程序以使用 Xcode 9

> 原文：<https://medium.com/oracledevs/upgrading-your-oracle-jet-hybrid-mobile-app-to-use-xcode-9-25ac104f524d?source=collection_archive---------1----------------------->

如果您一直在使用 Oracle JavaScript Extension Toolkit(JET)开发混合移动应用程序，并且最近升级到 Xcode 9，则首次部署到 iOS 设备时，您可能会看到以下错误:

```
Error Domain=IDEProvisioningErrorDomain Code=9 “”HybridBasic.app” requires a provisioning profile.” UserInfo={NSLocalizedDescription=”HybridBasic.app” requires a provisioning profile., NSLocalizedRecoverySuggestion=Add a profile to the “provisioningProfiles” dictionary in your Export Options property list.}
```

您的第一个想法可能是您的配置文件有问题，但实际上您需要做的是更新您的 Cordova 安装。

第一步是将您的 Cordova CLI 更新到最新版本:

```
$ npm -g install cordova@latest
```

如果你在 macOS 上，你需要以“sudo”的身份这样做。

npm 输出应该报告安装了什么版本，但是您可以通过键入以下命令来检查:

```
$ cordova --version
```

这应该报告 7.1.0 或更高版本。

然后在你现有应用的顶层文件夹中，你需要移除旧的 cordova-ios 并添加最新的:

```
$ ojet remove platform ios
$ ojet add platform ios
```

后一个命令将报告安装了哪个版本的 cordova-ios，但是要仔细检查，您可以键入:

```
$ ojet list platforms
```

这应该会报告 cordova-ios@4.5.3 或更高版本。

更新之后，构建或服务现在应该可以工作了:

```
$ ojet serve ios --device --build-config=buildConfig.json
```

当您构建新的 JET 混合移动应用程序时，它们将包含与您的 Cordova CLI 安装兼容的最新 cordova-ios 版本。

记得让你的工具保持最新，快乐编码！