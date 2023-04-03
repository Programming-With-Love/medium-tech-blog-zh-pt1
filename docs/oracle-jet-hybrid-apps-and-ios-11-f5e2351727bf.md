# Oracle JET 混合应用和 iOS 11

> 原文：<https://medium.com/oracledevs/oracle-jet-hybrid-apps-and-ios-11-f5e2351727bf?source=collection_archive---------2----------------------->

Oracle JavaScript Extension Toolkit(JET)混合入门模板允许在每个受支持的移动平台(Android、iOS 和 Windows)上使用状态栏，而无需您添加 cordova-plugin-statusbar 插件。

随着 iOS 11 和 cordova-ios 中最近的变化，您可能会发现您的 Oracle JET hybrid mobile 应用程序在 iOS 11 上的标题中显示了额外的填充，或者如果您使用彩色背景，您可能会在状态栏上看到旧 iOS 版本中不存在的白色背景。

解决这个问题似乎很简单。

更新 index.html 中的“viewport”元标签，使其包含“viewport-fit=cover ”,如下所示:

```
<meta name="viewport" content="viewport-fit=cover, width=device-width, initial-scale=1, maximum-scale=1, user-scalable=no"/>
```

我已经在少量设备上进行了测试，包括 iOS 10 和 iOS 11、Android，甚至在各种浏览器上提供应用程序。这个小小的改动似乎一切都很好，但是如果您在测试中发现任何问题，请告诉我。