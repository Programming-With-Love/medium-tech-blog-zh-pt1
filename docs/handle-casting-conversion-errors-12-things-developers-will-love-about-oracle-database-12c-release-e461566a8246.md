# 处理转换错误:开发人员喜欢的关于 Oracle Database 12c 第 2 版第 8 部分的 12 件事

> 原文：<https://medium.com/oracledevs/handle-casting-conversion-errors-12-things-developers-will-love-about-oracle-database-12c-release-e461566a8246?source=collection_archive---------2----------------------->

有时，您会希望将一个值转换为不同的数据类型。如果您的值与所需的类型不兼容，这可能会带来问题。

您可以使用我们之前讨论过的 [validate_conversion 函数](/oracledevs/verify-data-type-conversions-12-things-developers-will-love-about-oracle-database-12c-release-2-ad90c97de0e5)来解决这个问题。但是还有另一种方法。Cast 现在有一个“转换错误时默认”条款。

这指定了如果 Oracle 无法将表达式转换为您想要的类型，它将返回哪个值。例如，假设您试图将 varchar2 列转换为日期。但它恰好包含值“不是日期”。你会得到一个严重的错误:

```
select cast ( 'not a date' as date ) 
from   dual; ORA-01858: a non-numeric character was found where a numeric was expected
```

使用新的子句，您可以告诉 Oracle 返回一个“神奇的日期”,而不是抛出一个异常。例如:

```
select cast ( 
         'not a date' as date 
         default date'0001-01-01' on conversion error 
       ) dt 
from  dual; DT 
-------------------- 
01-JAN-0001 00:00:00
```

然后，您可以在代码中添加对这个神奇值的检查。

请注意，默认值必须与要转换的数据类型相匹配。所以如果你要转换日期，你不能返回一个字符串:

```
select cast ( 
         '01012010' as date 
         default 'not a date' on conversion error 
       ) dt 
from   dual; ORA-01858: a non-numeric character was found where a numeric was expected
```

与 validate_conversion 一样，cast 使用您的 NLS 设置作为默认格式。如果要覆盖这些，请将格式作为参数传递:

```
select cast ( 
         '01012010' as date 
         default '01010001' on conversion error, 
         'ddmmyyyy' 
       ) dt 
from   dual; DT 
-------------------- 
01-JAN-2010 00:00:00
```

这太棒了。但是乍一看，它似乎是有限的。毕竟你多久用一次 cast？如果你和我一样，答案是“很少”。

但是事情远不止如此！

转换错误条款也适用于其他转换函数。比如:

*   截止日期()
*   收件人号码()
*   to_yminterval()
*   等等。

这才叫*真的*有用！这些都是你一直在使用的功能。

因此，您可以像这样编写数据类型转换:

```
select to_date( 
         'not a date' 
         default '01010001' on conversion error, 
         'ddmmyyyy' 
       ) dt 
from dual; DT 
-------------------- 
01-JAN-0001 00:00:00
```

将这与 validate_conversion 结合起来，可以更容易地将表达式转换成新数据类型*。*

*全文原载于 2016 年 11 月 10 日*[*blogs.oracle.com*](https://blogs.oracle.com/sql/12-things-developers-will-love-about-oracle-database-12c-release-2)*。*