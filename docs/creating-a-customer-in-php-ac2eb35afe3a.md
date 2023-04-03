# 在 PHP 中创建客户

> 原文：<https://medium.com/square-corner-blog/creating-a-customer-in-php-ac2eb35afe3a?source=collection_archive---------0----------------------->

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们在 https://developer.squareup.com/blog[的新家](https://developer.squareup.com/blog)

如果你没有任何客户，你就不可能有生意，但是从一个现有的系统中导入客户可能是一件痛苦的事情，有时你需要能够以编程的方式这样做。在 PHP 中，我们的 [PHP SDK](https://github.com/square/connect-php-sdk) 使得使用一个简单的脚本创建这些客户变得相当容易:

代码首先加载随 [Composer](https://getcomposer.org/) 一起安装的 PHP SDK，然后设置帐户的访问令牌。然后您可以创建一个新的`CustomersApi`实例和一个新的对象，它将保存您试图上传的所有客户信息。最后，您用您的客户对象调用`createCustomer()`方法，瞧，您的帐户中有了一个全新的客户。

我希望你喜欢这篇文章，让我知道你希望下一篇文章是关于什么的，请联系 twitter 上的 [@SquareDev](https://twitter.com/SquareDev) 。