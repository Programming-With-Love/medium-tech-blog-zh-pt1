# Square Connect Node SDK 简介

> 原文：<https://medium.com/square-corner-blog/introducing-the-square-connect-node-sdk-4f2a30f46b6e?source=collection_archive---------3----------------------->

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

作为我们帮助开发人员创新、构建和创造的目标的一部分，开发人员平台团队很高兴地宣布 Square SDK 家族的新成员:Square Connect Node SDK。随着 Square Connect Node SDK 的发布，Square 现在提供了从 iOS 到 C#的八种不同的 SDK，以帮助开发人员在任何平台上轻松无缝地与 Square 集成。

# **使用 Node SDK 进行设置**

为 Node.js 应用程序启用 Square 可以通过三个简单的步骤来完成。

**第一步:**安装 SDK

Node.js SDK 适用于 6.0 或更高版本。如果您的应用程序满足这一要求，那么添加 SDK 就像在您的应用程序中添加 square-connect 包一样简单。

```
npm install square-connect --save
```

**步骤 2** :设置 Oauth2

现在 square-connect 已经添加到您的应用程序中，您需要添加授权。您的**访问令牌**和其他凭证可以在位于 [Square 开发者门户](https://connect.squareup.com/apps)的 Square 应用仪表板上找到。

```
// Import square-connect 
const SquareConnect = require('square-connect');
const defaultClient = SquareConnect.ApiClient.instance;// Configure Oauth2 access token for Authorizationconst 
oauth2 = defaultClient.authentications.oauth2;
oauth2.accessToken = 'YOUR ACCESS TOKEN';
```

**第三步**:创建

一旦安装了 square-connect 包并配置了 Oauth2，就可以进行第一次 API 调用了。

我们可以简单地通过初始化一个新的 API 客户端，然后调用一个以您想要的端点命名的函数来访问一个端点。

```
// Create a new Locations API Client
const locationsApi = new SquareConnect.LocationsApi();// Make an API call to the listLocations endpoint
locationsApi.listLocations()
  .then((response) => {    
    console.log('API called successfully, returned data: ' +
    response);
  });
```

Node.js SDK 和我们所有的其他 SDK 一样，是为了增强您的开发人员体验而创建的。通过添加 Node.js SDK，我们希望减轻开发人员的负担，并更容易利用 Square 平台的全部功能。

# 了解更多信息

*   SDK 的公开回购可以在 [Github](https://github.com/square/connect-javascript-sdk) 上找到。
*   要查看运行中的 SDK，请查看我们的示例[应用](https://github.com/square/connect-api-examples/tree/master/connect-examples/v2/node_payment)。
*   要了解更多关于 Square API 的功能，请查看我们的[文档](https://docs.connect.squareup.com/) **。**