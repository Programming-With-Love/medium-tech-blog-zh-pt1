# 科尔多瓦应用程序的新 WKWebView 插件

> 原文：<https://medium.com/oracledevs/new-wkwebview-plugin-for-cordova-apps-900282a21bdb?source=collection_archive---------0----------------------->

对于寻求 iOS 设备最佳性能的 Cordova 应用程序开发人员，我有一些激动人心的消息:

Oracle JET 团队今天发布了 Cordova-plugin-wkwebview-file-xhrCordova 插件的 v2，现在这是您需要获得 iOS 上的 wk webview 的性能优势的唯一插件，而没有阻止许多开发人员在其应用程序中使用该 web 视图的 CORS 限制。

要在您的 Oracle JET mobile 应用程序中包含此插件，只需输入:

*ojet 添加插件 Cordova-plugin-wk webview-file-xhr*

或者，如果您使用普通的 Cordova 或其他基于 Cordova 的框架，如 Ionic:

*cordova 插件添加 Cordova-plugin-wk webview-file-xhr*

此插件包括 Apache Cordova 团队的 Cordova-plugin-wkwebview-engine 插件，用于在您的应用程序中使用 wk webview 代替 UIWebView，并拦截您的应用程序的使用 file://协议或访问远程端点的 XHR GET 请求，通过本地层路由它们。

这种方法是安全的，并且避免了由代理或本地 web 服务器引入的开销。最棒的是，再也没有*加载资源失败:访问控制允许起源*错误不允许起源为空。

尝试一下，并在 [Oracle JET 社区](https://community.oracle.com/community/development_tools/oracle-jet)发表评论，让我们知道您的体验如何。