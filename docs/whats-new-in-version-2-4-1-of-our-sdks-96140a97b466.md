# 我们的 SDK 版本 2.4.1 有什么新功能

> 原文：<https://medium.com/square-corner-blog/whats-new-in-version-2-4-1-of-our-sdks-96140a97b466?source=collection_archive---------0----------------------->

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们在 https://developer.squareup.com/blog[的新家](https://developer.squareup.com/blog)

我们刚刚发布了客户端库的最新版本。以下是 2.3.0 版本的新增功能:

*   **支持 Apple Pay。**这个包括对相关数据模型和端点的支持。你可以在这篇博客文章中了解我们的 Apple Pay 网络支持。
*   **支持移动位置的 SDK。**现在，SDK 可以返回一个位置是物理的、实体的店面，还是移动的，如食品卡车。
*   **一个新的例子在** `**php**` **中自述。**有了这个例子，处理付款应该比以往任何时候都容易。你可以在这里查看:[https://github.com/square/connect-php-sdk](https://github.com/square/connect-php-sdk)
*   **对**`**java**`**&**`**ruby**`**的改进举例。我们更新了两种语言的示例，并添加了其他小的修正，比如对账户功能的改进描述。**
*   **从模型中删除了** `**readOnly**` **属性。**这修复了生成的 SDK 中阻止对象被反序列化的错误。

如果你对所有 SDK 的代码变化感兴趣，看看 GitHub 上完整的[差异。如果您注意到任何问题，或者有任何意见，请在](https://github.com/square/connect-api-specification/compare/4d565b932eba9b1b5b8bd6b10dbdfafed81fe14c...master) [Twitter](https://twitter.com/squaredev) 上告诉我们！