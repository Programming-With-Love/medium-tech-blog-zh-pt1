# 让您的客户与时俱进。

> 原文：<https://medium.com/square-corner-blog/keeping-your-customers-up-to-date-752245acbf82?source=collection_archive---------7----------------------->

## 了解如何在将客户与 Square 的 API 同步时利用一些新的 API 特性。

![](img/e97cf6c532a87216d5e70d0b42845fc5.png)

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们在 https://developer.squareup.com/blog[的新家](https://developer.squareup.com/blog)

Square customer management 通常可以满足您的所有需求，但有时您可能需要将您的客户目录与另一个客户数据库或订单管理系统保持同步。幸运的是，我们的 API 的一些新变化使得这比以往任何时候都更容易。

## 概观

在本帖中，我们将介绍 Square 中的客户目录和不同数据库之间的夜间同步用例。我们将使用一个本地 SQLite 数据库作为另一个数据库，将客户同步到 Node.js 进行所有编程。在高层次上，我们将使用 [SearchCustomers](https://docs.connect.squareup.com/api/connect/v2#endpoint-searchcustomers) 端点从我们的客户目录中查找最近更新和添加的客户，然后将它们插入到我们的 SQLite 数据库中。然后，我们将在另一端做同样的事情，将客户同步回 Square，这些客户可能已经通过其他方式独立添加到我们的其他数据库中。请记住，如果您使用 OAuth 访问属于 Square 卖家的数据，您必须获得他们的许可，才能访问或同步与他们的 Square 帐户相关的任何数据到不同的数据库。

## 设置:初始化本地数据库并获取 API 凭据。

多亏了`[sqlite3](https://www.npmjs.com/package/sqlite3)`包，设置我的本地数据库只需要几行 Node.js。我使用一个本地文件(`database.db`)来存储我的数据库和一个类似于客户对象响应体的表结构。

使用 Square 的 API 访问我的客户目录也同样简单，只要访问一下 [Square 开发者门户](https://connect.squareup.com/apps)，我就可以获得我的个人访问令牌，这将允许我访问我自己的 Square 帐户的所有 API 端点。我还将为 Square 的 API 使用 [JavaScript SDK。我可以用`npm install square-connect`和`require()`把它添加到我的应用程序中。](http://github.com/square/connect-javascript-sdk)

## 从 Square 获取最近添加和更新的客户。

有两种类型的信息变化，我们可能希望从我们的 Square 客户目录同步到我们的另一个数据库:新客户和更新了信息的客户。幸运的是，新的 SearchCustomers 端点使得通过尽可能少的 API 调用来获得这两类客户变得很容易。

为了获得最近创建的客户，我们将对 SearchCustomers 使用`created_at`过滤器。这允许我们过滤所有返回的结果，只过滤那些在我们指定的时间窗口内创建的结果。该代码查找最近一天内创建的客户，但也可以轻松地轮询最近 5 分钟内创建的客户，或者使用目录上次成功同步的时间戳作为窗口的开始。我们还可以使用带有`sort_field`参数的 ListCustomers 端点，按照创建日期列出我们的客户，然后遍历该列表，直到到达上次同步日期。搜索客户是一个更好的选择，因为它能够返回更多的客户条目，并且只返回指定时间窗口内的条目。

获得最近更新的客户也同样简单。我们将在请求体中为`updated_at`替换掉`created_at`参数，并指定相同的时间窗口。与使用 ListCustomers 端点和必须同步整个客户目录来查找任何更新相比，这是一个很大的改进。

这种方法有些幼稚，并且不处理分页。如果您在给定的时间窗口内同步超过 1000 个新创建或更新的客户，您将获得一个`cursor`参数，您将需要使用该参数来获得下一页结果。你可以在我之前的文章[*API 分页的技巧和诀窍*](/square-corner-blog/tips-and-tricks-for-api-pagination-5cacc6f017da) *中了解更多关于分页的知识。*

## 将客户同步到 Square

这里的基本情况也很简单，但是在实践中，对于您自己的数据库来说，可能会稍微复杂一些。我们可以选择不在 Square Customer 目录中的行，然后通过 CreateCustomer 端点运行它们。我们可以用许多不同的方式识别这些客户，一个`source`字段，还没有 Square id 的条目，或者查看自上次数据库同步以来创建的客户。

Selecting customers from the SQLite database and creating them inside Square

## 后续步骤

这只是我们的客户 API 中的新特性可以用于的一些有用情况的一个例子。请记住，在您自己的系统中实现这一点时，您可能需要考虑一些权衡。你应该每天、每周、每小时同步吗？您的数据模型将如何在系统间转换？您的实现会有所不同，这取决于您有多少客户，您将他们同步到哪里，以及您希望如何处理这些客户信息。如果您有任何问题，或者想要分享如何同步客户的示例，请随时回复此帖子，在 [**twitter**](https://twitter.com/squaredev) 或我们的 [**slack 社区**](http://squ.re/slack) 和 [**上告诉我们，注册我们的简讯**](https://www.workwithsquare.com/developer-newsletter.html?channel=Online%20Social&sqmethod=Blog) 以了解最新版本。