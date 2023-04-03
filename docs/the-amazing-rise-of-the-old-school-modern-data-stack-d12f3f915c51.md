# 传统现代数据堆栈的惊人崛起

> 原文：<https://medium.com/globant/the-amazing-rise-of-the-old-school-modern-data-stack-d12f3f915c51?source=collection_archive---------1----------------------->

![](img/d030f3c6074262bd37acedb0c40fef8d.png)

Charlie Chaplin in “*Modern Times” (1936)*

在不断变化的数据、分析和人工智能领域，今天最热门的词汇之一是[现代数据栈](http://continual.ai/post/the-future-of-the-modern-data-stack) (MDS)。这一系列云原生工具和技术旨在使设置和使用合适的数据平台变得更加容易。但这有什么大惊小怪的，这些技术的组成和特点是什么？为了回答这个问题，我们将深入一个典型的数据堆栈:*接收*、*存储*、*转换*和*输出*，并希望弄清楚它的现代版本是什么样子以及它是如何工作的。

**现代摄入**

![](img/479564dc96f9d948f025bfd0f9d1efbb.png)

数据管道的第一部分完全是关于连接，将数据从源获取到接收器。这些是[提取-转换-加载](http://en.wikipedia.org/wiki/Extract,_transform,_load) (ETL)的 E 和 L 部分，这是描述数据管道的一种常见方式。这种连接通常是用定制代码实现的，但 MDS 专注于现成的连接器。这通常以 NoOps SaaS ( [软件即服务](http://en.wikipedia.org/wiki/Software_as_a_service))的形式提供，即一种完全免费的服务。

*   [**five tran**](http://fivetran.com)**和[**Stitch**](http://stitchdata.com)**(现在是 **Talend** 的一部分)是专注于交付无争议、无代码数据集成的摄取平台的例子。它们也是不同定价模式的例子。Stitch 按接收的行数收费，而 Fivetran 对每个连接收取固定费用。****
*   ****[**Airbyte**](http://airbyte.io)**是一个新的有前途的开源摄取平台，类似于上面的 SaaS 选项。它可以在任何云提供商的容器上运行，但不受任何定价模型的约束。它还可以更加灵活地定制或制作自己的连接器。******
*   ******一些较大的数据公司也开始涉足这一领域。来自 **Databricks** 的 [**Delta Live 表**](http://databricks.com/product/delta-live-tables) 提供了一种创建 ETL 作业的声明式方法。这些可以用 Python 或 SQL 创建，然后在底层文件通知系统的帮助下运行。******

****由于有如此多不同类型的数据源，集成服务的列表还可以继续下去，但是让我们看一下存储选项。****

******现代存储******

****![](img/9f5f98dfbeef4d2ed1c43bbb4d49891b.png)****

****存储或数据入库由云数据仓库处理，例如[**【AWS 红移】**](http://aws.amazon.com/redshift)**Google Cloud 的**[**big query**](http://cloud.google.com/bigquery)**和 [**雪花**](http://snowflake.com) 。来自 Databricks 的 Delta Lake 也加入了这个俱乐部。这些选项比旧的数据仓库解决方案便宜得多，也更容易获得，旧的数据仓库解决方案通常需要在内部设置专门的硬件。基于云的解决方案可以提供按需定价模式，您只需为您使用的内容付费。这降低了进入数据仓库的门槛。**********

****这是 MDS 的核心部分，对许多人来说，是其他部分的先决条件和催化剂。如果没有相对便宜且可访问的云数据仓库，像[**【dbt】**](https://www.getdbt.com/)(不久将会涉及)这样的工具很可能不会出现在今天。****

******现代转型******

****![](img/0a700331f999267b6b8097449eb50c23.png)****

****转换步骤是提取-转换-加载的 T 部分(E **T** L)。在这里，通常使用 Apache Spark 等框架和 Java、Scala 和 Python 等编程语言对数据进行转换、清理和聚合等。有时，尤其是在数据仓库中，数据首先被装载到仓库中，然后被转换。在这种情况下，我们谈论 EL **T** ，这在 MDS 相当普遍。****

****现代转型的主导者是 **dbt** (数据构建工具)。它既有付费的云版本，也有开源的免费版本。dbt 依靠 SQL 来转换数据。事实上，没有其他选择，因为它将繁重的工作委托给实际的数据仓库引擎。dbt 为 stew 添加了一些“类似代码”的功能，例如 SQL 中没有的针对循环的条件逻辑，如*。这是用模板( [**金贾**](http://en.wikipedia.org/wiki/Jinja_(template_engine)) )完成的。它还带有用于测试、文档和版本控制的内置特性。*****

****除了使用 dbt，还有一种直接在数据仓库中进行转换的方法。这将是 SQL，但没有现代工具的新增功能。稍后，我们将更深入地探讨 SQL 的利与弊。****

******现代输出******

****![](img/e62f64b10c2453fa3325804999638b72.png)****

****MDS 的支持者主要来自分析和商业智能方面，因此输出或消费部分强调可视化和分析工具，而不是机器学习。但可以肯定的是，AI 和 ML 工作流也可以插入任何数据平台。****

****除了像 [**Tableau**](http://tableau.com) 和 [**Power BI**](http://powerbi.microsoft.com) (虽然 Power BI 只有 10 年历史)这样比较老牌的工具之外，现代可视化工具的例子还有 [**Mode**](http://mode.com) 和 [**Looker**](http://looker.com) 。这两个都是基于 SQL 的，也很容易使用。Looker(现在是 Google 的一部分)有自己的描述性语言[翻译成 SQL 查询。这里的目的是主要为业务用户简化 SQL 结构的复杂性。Mode 在分析方面更轻量级，但在协作方面更强大，提供用 SQL 或 Python/R 笔记本编写的交互式和可共享的报告。](http://docs.looker.com/data-modeling/learning-lookml/what-is-lookml)****

******MDS 的好处******

****MDS 的第一个好处是简单。上面提到的几乎所有技术都承诺某种“易用性”，可能是“无代码拖放”、“直观的 UI”或“自助服务”。这降低了学习曲线，反过来，可以提供快速设置，最终转化为更低的成本。****

****清单上的第二个是 SQL 的使用。这些解决方案中有许多是基于 SQL 的，甚至是纯 SQL 的。它的好处之一还是更加简单。SQL 是声明性的，可以说比成熟的编程语言更容易使用和学习。但主要的好处可能是它有更多的潜在用户。对于每一个精通 Java 或 Python 的数据工程师，我们都有几个数据分析师，如果有合适的工具，他们可能会用 SQL 建立类似的数据管道。这在我们目前永远缺乏数据工程师的情况下非常重要。****

****最后，使用 MDS 需要更低的前期成本。这可能就是为 SaaS 每月支付无附加条件的账单，或者购买并拥有一台大型计算设备的区别。这种好处还与所涉及的技能成本有关，比如用 Scala 构建一个定制应用程序或者购买现成的东西。****

******MDS 的弊端******

****到目前为止，这个论点的确令人印象深刻:MDS 可以比替代的传统堆栈更快、更便宜地交付结果。但当然，也有不利之处。****

****其中一个是，嗯，我们刚刚在上面称赞的那个老 SQL。关于这项已有 40 年历史的技术的利与弊已经说了很多(这里的[是一个有趣的例子)。SQL 不是一种通用编程语言，它是一种为关系模型构建的特定领域语言(DSL)。这带来了一些“编程”限制，并且通常降低了处理更复杂数据需求的灵活性和能力。](http://scattered-thoughts.net/writing/against-sql)****

****还有一个工装问题。与任何现代编程语言相比，SQL 对调试、测试和版本控制没有相同的支持。改进这个工具集是像 dbt 这样的框架的目标之一。****

****MDS 的另一个问题是处理敏感数据，比如个人身份信息( [PII](https://en.wikipedia.org/wiki/Personal_data) )。敏感数据通常必须在加载到数据仓库之前进行*处理，因此 ELT 可能不是一个选项。此外，[匿名化](https://en.wikipedia.org/wiki/Data_anonymization)敏感数据的不同方式可能需要比现成连接器提供的更多的定制处理。*****

******结论******

****几乎可以肯定的是，MDS 列车将继续行驶——我们可以看看 dbt 和 Airbyte 等技术目前的势头。像“[分析工程师](https://dataform.co/blog/what-do-analytics-engineers-do)”这样的新角色已经出现在数据领域，我们也不断听到“数据分析师的[崛起”。](https://quanthub.com/data-analysts/)****

****尽管我们已经看到了一个从输入到输出的“完整”数据堆栈，但是还有更多。新的组件和层被添加到堆栈中。 [*反向 ETL*](/memory-leak/reverse-etl-a-primer-4e6694dcc7fb) 涵盖了从数据仓库到更专业的第三方系统的流程。这也触及到了 [*数据共享*](http://gartner.com/smarterwithgartner/data-sharing-is-a-business-necessity-to-accelerate-digital-business) 的趋势。然后有很多关于 [*控制层*](http://trackplan.io/blog/governance-for-the-modern-data-stack) 的讨论，增加了 *ETLG* 作为另一个管道变体。****

******延伸阅读******

****一个优秀的初始[指南](http://towardsdatascience.com/the-beginners-guide-to-the-modern-data-stack-d1c54bd1793e)到 MDS 的世界。****

****其主要支持者之一给 MDS 上的历史课。****

****有趣的[分析](http://mattturck.com/data2020)当前的数据和人工智能景观，包括 MDS。****