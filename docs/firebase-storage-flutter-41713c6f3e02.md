# 燃料库储存——颤振💙💛

> 原文：<https://medium.com/google-developer-experts/firebase-storage-flutter-41713c6f3e02?source=collection_archive---------2----------------------->

将图像和文件存储到您的服务器是一项繁忙的任务。但是 Firebase 存储很大程度上解决了这个问题！让我们看看如何使用它！

![](img/8214daa7d381ea7c176d552c4e69a713.png)

Firebase 是提供许多功能的服务之一，您可以在 web 和移动应用程序中使用这些功能！最初，当我们想要在数据库中存储图像和文件时，这是一项非常繁忙的任务！Firebase Storage 是一项服务，允许您轻松地将文件和图像存储到云存储中，并且代码量明显减少！

![](img/3fdb37228fae65612f8aa28d505943ed.png)

从 Firebase 存储开始，需要执行以下步骤:

*   从控制台启用存储。
*   为不同的存储操作创建代码。

## 步骤 1:从控制台启用存储

去 [Firebase 控制台](https://console.firebase.google.com/)打开你的项目。在右侧面板上，单击存储选项，它会将您带到欢迎/介绍页面，您可以在该页面上单击`Get Started`按钮！按照接下来的步骤，启用 Firebase 存储。

![](img/3fdb37228fae65612f8aa28d505943ed.png)

## 步骤 2:将文件上传到存储器

Firebase 存储代码非常短，很容易做到。让我们看看如何为 Firebase 存储添加上传操作！

您可以通过以下任一方式上传文件:

*   uint 8 列表
*   从一条小路
*   从本地文件

在这里，我们已经上传了 Uint8List 格式的图片！您可以看到代码只有一行，这将使用提供的文件名将文件上传到您的存储桶！

## 第三步:获取下载网址

一旦上传了图像，就可以获得该图像的下载 URL，这样就可以使用 NetworkImage 来显示特定的图像。

## 步骤 4:删除文件

嗯，把不需要的文件放在仓库里是没用的。当用户被删除时，删除文件总是一个好的做法。

因此，您的整个服务文件将如下所示:

就是这样！！！

# 希望你喜欢这篇文章！

你可以通过克隆 [GitHub 库](https://github.com/AbhishekDoshi26/firebase_starter/tree/realtime_database)来尝试一下！

疑惑？随意留言 [@AbhishekDoshi26](https://linktr.ee/abhishekdoshi26)

![](img/b2a339056ca3a0e6d2806aa198e5e2a6.png)

> 不要停止，直到你呼吸！💙Abhishek Doshi