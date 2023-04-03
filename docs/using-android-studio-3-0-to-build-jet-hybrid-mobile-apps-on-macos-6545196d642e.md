# 使用 Android Studio 3.0 在 macOS 上构建 JET 混合移动应用

> 原文：<https://medium.com/oracledevs/using-android-studio-3-0-to-build-jet-hybrid-mobile-apps-on-macos-6545196d642e?source=collection_archive---------2----------------------->

我总是鼓励 Oracle JavaScript Extension Toolkit(JET)混合移动应用程序开发人员保持他们的移动工具包是最新的，但是偶尔会出现一些问题。

今天我更新到 Android Studio 3.0，发现像`ojet build android, ojet serve android`甚至`ojet clean android`这样的命令不再工作了，出现了一个无益的错误:

```
Error: spawn EACCESError: Something in cordova failed. child process exited with code: 1
```

发生这种情况是因为 Cordova 使用了打包在 Android Studio 中的`gradle`可执行文件，而谷歌改变了 Gradle 在 macOS 上的 Android Studio 3.0 中的打包方式。

[这个问题](https://issues.apache.org/jira/browse/CB-13495)已经针对 Cordova-Android 提出，并声称在 cordova-android@6.4.0 中得到[修复，但修改我的 JET hybrid 应用程序以使用这个 Android 平台版本对我没有帮助，所以我想我应该分享一下对我有效的修复:](http://cordova.apache.org/announcements/2017/11/09/android-release.html)

```
chmod +x /Applications/Android\ Studio.app/Contents/gradle/gradle-4.1/bin/gradle
```

这个命令是安全的，因为它只是给你对 Android Studio 3.0 中打包的`gradle`可执行文件的“执行”权限。

这个技巧不仅适用于 JET 混合移动应用程序开发人员，也适用于任何使用 Cordova 或其他基于 Cordova 的框架的开发人员。