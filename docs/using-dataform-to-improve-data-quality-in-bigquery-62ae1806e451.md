# 使用数据表单提高 BigQuery 中的数据质量

> 原文：<https://medium.com/compendium/using-dataform-to-improve-data-quality-in-bigquery-62ae1806e451?source=collection_archive---------0----------------------->

如果你开始一个数据驱动的项目，每个人都会告诉你一件事:你*将*花*大部分时间*处理*数据质量问题*。在处理从传统或广告挂钩系统收集的数据时尤其如此。您将遭受相当大的数据质量问题，因为通常情况下，对于来自源系统的数据输入，存在不清楚甚至未定义的规则。花费在创建和发现减轻这些质量问题所需的数据清理规则上的时间对于任何大规模的项目来说都是相当大的时间消耗，并且涉及到组织的大部分。这是[数据网格模式转变](https://martinfowler.com/articles/data-monolith-to-mesh.html)的主要驱动因素之一，在这种模式中，您认为数据是一种产品，并迫使数据源首先提供良好的数据质量，您知道，这是一件非常好的事情！然而，这篇文章并不是要实现一个新的架构来解决未来的问题。*现实就是现在，数据质量还是你要处理的事情*。

![](img/fcd21f85f84c414224a6e791ce51649a.png)

过去几年的趋势是 ELT 超过 ETL，在这种情况下，转换发生在数据库级别——它比从数据库中取出数据再放入数据库更快、更便宜，并且需要的基础设施更少。GCP 最近收购的一个产品是 data form——一个协调你的转换的工具——或者你可以称之为 SQL 脚本。视图和表被抽象成实体，数据形式将帮助你以正确的顺序执行一切，并具体化为视图、表或概念。这绝不是什么新鲜事: [Dbt](https://www.getdbt.com/) 这么做已经好多年了！考虑到 dataform 将被集成到 [BigQuery](https://cloud.google.com/bigquery/) 中，我将选择该产品来告诉您在该平台上可以做些什么。

Dataform 还附带了一种通用编程语言:nodejs 形式的 javascript。这意味着您可以通过编程做(几乎)任何事情来创建您的 sql 脚本。例如，很容易从源系统中为每个表创建一个视图或历史表，我们将很快对此进行研究。

# 处理字符输入

字符输入可能是任何数据科学家的噩梦，因为你基本上可以输入任何东西。作为一个例子，让我们来看一个处理电话号码的数据库。在挪威，写电话号码至少有三种主要方式:“123 45 678”、“12 34 56 78”、“12345678”，国家代码可以是“+47”或“0047”。还有一百万种其他的写法。

当处理字符输入时，你应该做的第一件事是了解你的输入有不同的格式。通过获取表中的事实，您可以合理地判断需要创建多少规则，并且最有可能确定超出规则修复范围的数据格式。它还可以让你很快识别异常值，你会对一些输入感到惊讶！

如果我们用字母“d”代替每个数字，我们将得到“ddd dd ddd”和“DD DD DD DD DD”(可能还有更多)。然后，我们可以按格式和计数进行分组，以计算出变化的频率，并且我们将确切地知道实施什么清理规则来减轻问题，并且还可以找到我们不能减轻的问题。例如，空值将是很难的，或者带有文本数据的电话号码，如“是”、“我有”、“不告诉”将不可能转换成数字。例如，您可以将文本替换为“w ”,以便为这些内容创建一个类别。

执行此操作的 SQL 可能如下所示:

```
REGEXP_REPLACE(REGEXP_REPLACE(REGEXP_REPLACE(REGEXP_REPLACE(REGEXP_REPLACE(REGEXP_REPLACE(PHONE, r”[a-zA-Z]+”, “w”), r”\d{5,}”, “d+”), r”\d{4}”, “dddd”), r”\d{3}”, “ddd”), r”\d{2}”, “dd”), r”\d{1}”, “d”) as PHONE_profile,
```

> 如果你有更好的解决方案来实现同样的事情，请在评论中告诉我！

# 数据形式来拯救

请记住，数据表单可以自定义编程，以帮助您创建所需的 SQL。

例如，采用任何表并对字符列应用分析洞察力的实用程序脚本可能如下所示:

```
function profile_table_columns(source, targetSchema, targetTable, columns) {
   var profile_col_list = “”;
   var first = true;

   for (const i in columns) {
      var c = columns[i]
      if (!first) {
         profile_col_list += “,”
      } else {
         first = false
      }
      profile_col_list += ‘REGEXP_REPLACE(REGEXP_REPLACE(REGEXP_REPLACE(REGEXP_REPLACE(REGEXP_REPLACE(REGEXP_REPLACE(‘ + c + ‘, r”[a-zA-Z]+”, “w”), r”\\d{5,}”, “d+”), r”\\d{4}”, “dddd”), r”\\d{3}”, “ddd”), r”\\d{2}”, “dd”), r”\\d{1}”, “d”) as ‘ + c + ‘_PROFILE‘
   }publish(targetTable).schema(targetSchema).query(ctx => `SELECT *, ${profile_col_list} FROM ${ctx.ref(source)}`).type(“view”);}
```

该函数将源表作为输入，并有一个放置它的目标表。它还需要一个列列表来为其生成分析代码。

对于您想要生成分析视图的每个表，它看起来像这样:

```
data_profile.profile_table_columns(
   {schema: 'example_schema', name: ‘PHONE_TABLE’},
   “table_pofiles”,
   “PHONE_PROFILE”,
   [‘PHONE’, 'COUNTRY_CODE', ...]
)
```

这将在 table_profiles.phone_profile 位置创建一个包含以下内容的视图:

```
select *, REGEXP_REPLACE(REGEXP_REPLACE(REGEXP_REPLACE(REGEXP_REPLACE(REGEXP_REPLACE(REGEXP_REPLACE(PHONE, r"[a-zA-Z]+", "w"), r"\d{5,}", "d+"), r"\d{4}", "dddd"), r"\d{3}", "ddd"), r"\d{2}", "dd"), r"\d{1}", "d") as PHONE_PROFILE FROM example_schema.PHONE_TABLE
```

# 理解数据

这个 SQL 基本上是从源表中选择所有内容，但是也将包含电话的列转换成一种格式而不是值。然后，大多数 BI 工具可以排列计数，并使用过滤器可视化值和格式。使用这个视图创建一个带有历史记录的表来跟踪一段时间内的变化也是一个小小的步骤，这样您就可以看到数据质量的趋势。结果可能如下所示:

```
format       count
ddd dd ddd     700 (looks well formatted)
dd dd dd dd    500 (looks well formatted)
dddddddd      1100 (looks well formatted)
w+              26 (text?)
ddw dd ddd       1 (a misspelled 0 as o)
dddd             1 (possibly not a number)
+dd dddddddd     4 (includes country code?)
etc.
```

# 包扎

有了这些知识，您可以开始减轻数据质量问题:创建清理规则、设置警报和监控，并建立到源系统的反馈循环，以便它们可以修复您无法修复的问题。更重要的是，你将能够对你必须投入的时间和精力有一个合理的估计！

您需要了解数据表单中的一些限制和设计决策。与 dbt 不同，dataform 是完全沙盒化的，因此您不能使用数据库生成 SQL。也就是说，您不能通过查询 INFORMATION_SCHEMA 来生成查询。

如果我有时间，我会发布一个数据质量包到 dataform，这样这些实用函数就可以使用了——它们非常方便！但是现在，我真的希望它能激发一些灵感，告诉你如何自动化数据质量监控，在我看来，这是 dataops 的一个重要部分！