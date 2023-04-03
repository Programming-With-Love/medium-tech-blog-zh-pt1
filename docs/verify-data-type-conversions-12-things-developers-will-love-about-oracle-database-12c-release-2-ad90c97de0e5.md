# 验证数据类型转换:开发人员会喜欢的关于 Oracle Database 12c 第 2 版第 7 部分的 12 件事

> 原文：<https://medium.com/oracledevs/verify-data-type-conversions-12-things-developers-will-love-about-oracle-database-12c-release-2-ad90c97de0e5?source=collection_archive---------5----------------------->

这是那些太常见的问题之一。[验证日期确实是日期](https://asktom.oracle.com/pls/apex/f?p=100:11:0::::P11_QUESTION_ID:9528368000346452927)。

造成这种情况的主要原因是将日期存储为字符串的糟糕做法。最大的问题之一是让人们能够在“日期”列中存储明显不是日期的东西:

```
create table dodgy_dates ( 
  id int, 
  is_this_a_date varchar2(20) 
); insert into dodgy_dates values (1, 'abc');
```

以及一大堆可能是日期的值。如果您提供了正确的格式掩码:

```
insert into dodgy_dates values (2, '20150101'); 
insert into dodgy_dates values (3, '01-jan-2016'); 
insert into dodgy_dates values (4, '01/01/2016');
```

只返回有效日期是很棘手的。如果您尝试使用 to_date()转换所有内容，您会得到异常:

```
select t.* from dodgy_dates t 
where to_date(is_this_a_date) < sysdate; ORA-01858: a non-numeric character was found where a numeric was expected
```

或者也许:

```
ORA-01861: literal does not match format string
```

或者

```
ORA-01843: not a valid month
```

![](img/b2c104352d47ab9c339e84f98c126e07.png)

您可以通过编写自己的 is_date()函数来解决这个问题。或者，如果你真的很勇敢，使用正则表达式。

无论哪种方式，都是很多不必要的工作。

因此，为了让您的生活更轻松，我们创建了一个新函数 validate_conversion。您传递给它一个值和一个数据类型。然后，Oracle 数据库会告诉您它是否可以进行转换。如果可以，它返回一个。否则你得零分。

要返回表中可能是实际日期的行，请将其放在 where 子句中:

```
select t.* from dodgy_dates t 
where validate_conversion(is_this_a_date as date) = 1; ID         IS_THIS_A_DATE 
---------- -------------------- 
3          01-jan-2016
```

没有错误。但是第二排和第四排去哪里了呢？他们也可能是约会对象！

Validate_conversion 一次只测试一种日期格式。默认情况下，这是您的 NLS 日期格式

每个客户端都可以设置自己的格式。所以如果你依靠这个，你可能会得到意想不到的结果…

为了避免这种情况，我强烈建议您将格式作为参数传递。例如:

```
select t.* from dodgy_dates t 
where  validate_conversion(is_this_a_date as date, 'yyyymmdd') = 1; ID         IS_THIS_A_DATE 
---------- -------------------- 
2          20150101
```

因此，要返回所有可能的日期，您需要多次调用这个函数:

```
select t.* from dodgy_dates t 
where validate_conversion(is_this_a_date as date, 'yyyymmdd') = 1 or
  validate_conversion(is_this_a_date as date, 'dd/mm/yyyy') = 1 or 
  validate_conversion(is_this_a_date as date, 'dd-mon-yyyy') = 1; ID         IS_THIS_A_DATE 
---------- -------------------- 
2          20150101 
3          01-jan-2016 
4          01/01/2016
```

这不仅仅是为了约会。您可以对以下任何数据类型使用 validate_conversion:

*   binary_double
*   二进制 _ 浮点
*   日期
*   日秒间隔
*   年与月的间隔
*   数字
*   时间戳
*   带时区的时间戳

如果您想将字符串转换成日期，那么在 select 中需要类似的逻辑。这将针对各种格式掩码测试表达式。如果匹配，使用相关掩码调用 to_date:

```
case 
  when 
    validate_conversion(is_this_a_date as date, 'yyyymmdd') = 1 
    then to_date(is_this_a_date, 'yyyymmdd') 
  when 
    validate_conversion(is_this_a_date as date, 'dd/mm/yyyy') = 1 
    then to_date(is_this_a_date, 'dd/mm/yyyy') 
  when 
    validate_conversion(is_this_a_date as date, 'dd-mon-yyyy') = 1 
    then to_date(is_this_a_date, 'dd-mon-yyyy') 
end
```

【blogs.oracle.com】全文原载于 2016 年 11 月 10 日[](https://blogs.oracle.com/sql/12-things-developers-will-love-about-oracle-database-12c-release-2)**。**