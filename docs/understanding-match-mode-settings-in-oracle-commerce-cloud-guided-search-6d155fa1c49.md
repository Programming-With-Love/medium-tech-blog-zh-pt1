# 了解 Oracle Commerce 云指导搜索中的匹配模式设置

> 原文：<https://medium.com/oracledevs/understanding-match-mode-settings-in-oracle-commerce-cloud-guided-search-6d155fa1c49?source=collection_archive---------9----------------------->

![](img/46129f3a0bd6ce1e6eb96c7ab045170d.png)

在 Oracle Commerce Cloud Guided Search 中检索结果的第一步是在给定的搜索界面中匹配关键字。例如，如果购物者输入多个搜索词，可能会出现不同级别的匹配。

默认情况下，名为“matchallpartial”的匹配模式控制匹配。使用 matchallpartial，它首先尝试将所有输入的术语与搜索界面中的字段进行匹配。如果找不到任何结果，那么就只能进行部分匹配。这将返回包含搜索词总数的子集的结果。

让我们来看一些使用默认设置的示例:

*   如果用户输入 1 个术语，它将必须匹配该术语
*   如果用户输入两个词，默认情况下，它必须匹配这两个词
*   如果用户输入 3 个词，它首先尝试匹配所有 3 个词。如果找不到任何结果，它将返回与两个词的不同组合相匹配的结果。
*   对于 4 个学期来说，都差不多。首先，它尝试所有 4 个。如果没有匹配，它将返回 3 个词的组合的结果。

注意:一般来说，根据我们网站收集的分析，大多数购物者只输入 1 或 2 个词，有时是 3 个。购物者很少输入 4 个或更多的术语。因此，针对 1、2 和 3 期方案进行优化是有意义的。

# 改变你的比赛模式

引导式搜索具有更改匹配模式的选项，尽管我们建议使用上面记录的默认 matchallpartial。要考虑的一些其他匹配模式:

*   matchall:这需要匹配所有术语。如果有零个，那就这样。
*   matchpartial:这需要始终匹配术语的子集。这会给你更多的结果。
*   matchpartialmax:这是 matchallpartial 的一个有趣的替代方案。它首先尝试匹配所有术语，如果没有找到任何记录，则按降序尝试组合。例如，如果输入了 5 个没有结果的词，那么它将尝试 4 个词的组合。如果仍然没有结果，则组合 3，依此类推。如上所述，然而，大多数购物者并没有输入 4+项。

没有用于更新匹配模式的配置。相反，它必须作为`Ntx=`参数放在 URL 上。一个例子:`&Ntx=mode matchall`

# 更改您的部分匹配设置

考虑到上面提到的默认行为，当购物者输入两个词时，它必须匹配这两个词。这可以防止在默认情况下返回可疑的结果。假设您正在搜索“blue shirts”，但是没有“shirts”本身的匹配项。默认设置阻止只返回“蓝色”的结果。

但是，您可能希望更改此默认行为。您可以调整几个设置:

*   **maxWordsOmmitted** :指定用户查询中可以忽略的最大术语数。如果“最多忽略”值设置为零，则可以忽略任意数量的单词。默认情况下，该值设置为 1。
*   **minWordsIncluded** :指定每个结果必须匹配的最少术语数。如果原始查询中没有足够的词来满足这个规则，那么整个查询必须匹配。默认情况下，该值设置为 2。

在我们的 2 个搜索项的例子中， **minWordsIncluded** =2 的默认值是防止只匹配 1 个搜索项的结果被返回。

在我们的 4 个搜索词的例子中， **maxWordsOmmitted=1** 的默认值就是为什么返回 3 个结果的组合，而不是 2 个。

默认情况下，您的站点使用/GS admin/v1/cloud/search interfaces/All 作为搜索界面。

您可以执行以下步骤来更改上述设置:

1.  GET/GS admin/v1/cloud/search interfaces/All
2.  创建“partial match”JSON 配置
3.  PUT/GS admin/v1/cloud/search interfaces/All
4.  POST/GS admin/v1/cloud/tasks { " type ":" publish " }以促进更改。或者，如果您发布了目录更改，下一个索引作业将负责提升这些更改。

下面是一个例子，购物者输入 2 个术语，配置将允许返回 1 个术语的部分匹配:

```
{
    "ecr:type": "search-interface",
    "crossFieldMatch": "always",
    "partialMatch" : {
        "maxWordsOmmitted" : 1,
        "minWordsIncluded" : 1
    },
    "fields": [
       {
          "attribute": "product.displayName"
       },
       {
          "attribute": "product.category"
       }
}
```

# 最后的想法

查看您的搜索指标和报告，了解您网站上的购物者行为是什么样的，这总是一个好主意。小心不要仅仅为了修复一两个场景就推出搜索配置更改。请记住，还有其他选项来调整您的搜索结果。

*   关键词重定向
*   动态监管规则
*   专门助推和掩埋产品

还要注意，匹配模式只控制返回哪些产品。它不*而*控制搜索结果的排序。这就是所谓的相关性排名，也是上一篇文章《[了解和控制 Oracle 商务云搜索结果的顺序](/oracledevs/understanding-and-controlling-the-order-of-search-results-in-oracle-commerce-cloud-813359864c0d)》中涉及的主题

# OCI 自由层

顺便说一句，如果你进入了 OCI 或者还没有开始体验它，[注册一个 OCI 免费层帐户](https://signup.cloud.oracle.com/?language=en&sourceType=:ex:tb:::::&SC=:ex:tb:::::&pcode=)无限期地使用诸如块存储、数据库和计算之类的服务。

# 加入对话！

如果你对 Oracle 开发人员在他们的自然环境中发生的事情感到好奇，就来参加我们的公共休闲频道吧！我们不介意成为你的鱼缸🐠

*照片由* [*萨夫*](https://unsplash.com/@saffu?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) *上* [*下*](https://unsplash.com/s/photos/loupe-cloud?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)