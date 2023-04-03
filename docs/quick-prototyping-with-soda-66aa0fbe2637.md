# 用苏打快速制作原型

> 原文：<https://medium.com/oracledevs/quick-prototyping-with-soda-66aa0fbe2637?source=collection_archive---------5----------------------->

# 简单的 Oracle 文档访问

![](img/006089ed3097f79fcf6a73f9318c9405.png)

Photo by [Food Photographer David Fedulov](https://unsplash.com/@phototastyfood?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

我想和你分享一下，你可以如何加快一个黑客马拉松的应用程序的实现，你的小宠物项目或者你的$1B 应用程序的想法。没关系。

精益创业是一种众所周知的方法，当未知很大时，它可以开发出创新的想法。

底线是通过快速迭代和测试来缩短产品开发周期。

如果你想成功的话，你的酷应用程序将会有很多 CRUD
(创建、读取、更新和删除)操作，这些操作都是针对变化非常快的业务实体的。

根据我的经验，每个人都知道如何实现 RESTful API，但关键是如何存储和查询所有不同的实体，为用户带来真正的价值。

我们应该首先考虑集成各个部分，而不是每个细节的实现细节。

我将创建一个简单的 API，包含安全性、最佳实践、强大的查询语言——所有这些都无需编写一行代码。

# 尝试一下

您可以在几分钟内试用它—无需在您的计算机上安装或配置任何东西。在 Oracle Cloud 上免费创建一个 **Oracle 自治数据库**。您可以使用 **SQL Developer Web** 执行所有 SQL 语句。

**您有甲骨文云账户吗？**如果没有，您可以在这里创建一个:

> *请记住，除非您明确要求按需付费升级，否则 Oracle Cloud 不会向您收取任何费用。没问题。*
> 
> [*甲骨文云免费注册*](http://bit.ly/34TzwGf)

像我在视频中做的那样创建免费的自治数据库；此外，您还可以熟悉 SQL Developer Web:

打开安装了 [Rest 客户端](https://marketplace.visualstudio.com/items?itemName=humao.rest-client)扩展的[可视代码](https://code.visualstudio.com/)。创建一个名为`books.http`的新文件，内容如下:

我们需要修改文件顶部的`@db_url`和`@password`变量。密码是您在创建时设置的。可以找到数据库 URL，如本视频所示:

必须修改 URL，所以它看起来像这样:

`YIR3R5Y0IWWLVII-DEMO.adb.eu-frankfurt-1.oraclecloudapps.com`。

基本上，你必须去掉开头的`https://`和结尾的`/ords/`。

按照下面的视频步骤学习如何创建新实体、插入、查询和删除新图书集合中的项目。

# 想了解更多？

如果您喜欢您正在阅读的内容，并有兴趣与我们的团队一起迎接这些挑战，请查看我们关于许多主题的免费培训课程，如空间和图形、文档数据库、APEX、数字助理等。下一个项目是

> [*检查下一次在融合数据库上的训练*](https://bit.ly/3ub8mr4)

专家和甲骨文云倡导者定期举办免费培训，时长约 1 小时。这些是教师指导的培训。您可以跟随我们的团队现场解决您的问题。

敬请关注更多关于 Oracle 云的精彩文章。

> *我是维克多·马丁，一名软件开发人员。我部署在 Oracle 云基础架构上。*
> 
> *随时在*[*LinkedIn*](https://bit.ly/3bRk83W)*上与我联系。*
> 
> 我也对潜水和太空工程感兴趣。乐意帮忙，一切都比火箭科学容易！