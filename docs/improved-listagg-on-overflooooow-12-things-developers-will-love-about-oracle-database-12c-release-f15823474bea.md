# 改进的 Listagg on Overflooooow:开发人员会喜欢的关于 Oracle Database 12c 第 2 版第 4 部分的 12 件事

> 原文：<https://medium.com/oracledevs/improved-listagg-on-overflooooow-12-things-developers-will-love-about-oracle-database-12c-release-f15823474bea?source=collection_archive---------3----------------------->

我们已经看到了[更长的对象名](/oracledevs/loooooooooooooooong-names-12-things-developers-will-love-about-oracle-database-12c-release-2-part-b5f6e79698)如何导致[变量声明](/oracledevs/pl-sql-constants-for-data-type-lengths-12-things-developers-will-love-about-oracle-database-12c-cb5f4d7c2d06)的问题。但这也可能带来更微妙的问题。例如，下面的查询为模式中的每个表返回一个逗号分隔的索引列表:

```
select table_name, 
       listagg( index_name, ',' ) 
         within group ( order by index_name ) inds 
from   user_indexes 
group  by table_name;
```

这一切都很好。但是这有一个潜在的问题。 [Listagg](http://docs.oracle.com/database/121/SQLRF/functions101.htm) ()返回一个 varchar2。这被限制为 4000 字节(如果您使用的是[扩展数据类型](https://oracle-base.com/articles/12c/extended-data-types-12cR1)，则为 32767 字节)。

所以在 12.1 和 11.2 中，在遇到问题之前，一个表上需要 130 个或更多的索引。如果在一个表上有那么多索引，那么比达到这个限制更大的问题就来了！

![](img/50460e273e9eb3c4fb7458b673d75b66.png)

但这在 12.2 中有所改变。对于较长的名称，一个表上的索引可能会超过 30 个。尽管这个数字仍然很大，但这似乎是合理的。特别是在报告数据库和数据仓库方面。您可以肯定，某个地方的某个人将开始创建“自我记录”的索引，如:

```
create index 
  reducing_the_monthly_invoice_run_     
  from_four_hours_to_three_minutes_ 
  PROJ12345_make_everything_faster_ 
  csaxon_thanks_everyone_yeah_baby on ...
```

创建太多这种类型，您的 listagg 查询将抛出令人沮丧的 ORA-01489 错误。

要绕过这一点是很棘手的。所以在 12.2 中我们增加了一个溢出子句。要使用它，请在分隔符后放置“溢出时截断”:

```
select table_name, 
       listagg( index_name, ',' on overflow truncate ) 
         within group ( order by index_name ) inds 
from   user_indexes 
group  by table_name;
```

有了这些，您的输出将看起来像这样，而不是一个异常:

```
...lots_and_lots_and_lots,of_indexes,...(42)
```

末尾的“…”表示输出大于 Oracle 可以返回的值。括号中的数字表示 Oracle 从结果中删除的字符数。因此，你不仅可以看到有更多的数据，你还可以得到有多少数据的指示。

完整的语法是:

```
listagg ( 
  things, ',' [ on overflow (truncate|error) ] 
  [ text ] [ (with|without) count ] 
) within group (order by cols)
```

现在，您可以明确地说出您想要错误还是截断语义。很有可能您已经编写了代码来处理 ORA-1489 错误。因此，为了保持代码的行为不变，缺省值仍然是 error。

text 和 count 子句控制出现在字符串末尾的内容。如果您想用“更多”、“额外”或“点击更多”超链接替换“…”，只需提供您的新字符串！

```
select table_name, 
       listagg( index_name, ',' on overflow truncate 
         '<a href="http://www.showfulldetails.com">click here</a>' 
       ) within group ( order by index_name ) inds 
from   user_indexes 
group  by table_name;
```

您也可以通过指定“不计数”来删除修剪的字符数。

*全文原载于 2016 年 11 月 10 日*[*【blogs.oracle.com】*](https://blogs.oracle.com/sql/12-things-developers-will-love-about-oracle-database-12c-release-2)*。*