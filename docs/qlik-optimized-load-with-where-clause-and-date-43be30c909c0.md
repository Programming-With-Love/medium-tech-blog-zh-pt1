# 带有 where 子句和日期的 Qlik 优化装载

> 原文：<https://medium.com/quick-code/qlik-optimized-load-with-where-clause-and-date-43be30c909c0?source=collection_archive---------0----------------------->

![](img/7114d5e338acad3fb56286f13cfe662a.png)

经常需要部分加载一个巨大的 QVD，它包含了你活着的时候的历史数据。

玩笑归玩笑，有一种奇妙的方法可以做到这一点，而无需编写大量代码，并且负载得到优化。

假设您有一个 2017 年的历史 QVD，其中包含 5 亿条记录。因此，不使用 Where date _ field > = ' 2017–05–01 '，您可以创建一个表，该表将为您自动生成日期范围，然后您可以使用 Where EXISTS 加载您的 QVD，然后*瞧，*您将获得优化的加载，从而节省服务器资源并大大减少重新加载时间。

```
LET vDateMin = AddMonths(Today())-60; //5 Years from now
LET vDateMax = Today();TMP_DATE_TABLE:
LOAD
 Date(Date(‘$(vDateMin)’)+IterNo()-1) as Sales_Date
 AutoGenerate(1)
 While Date(Date(‘$(vDateMin)’)+IterNo()-1) <= Date(‘$(vDateMax)’); 

Sales:
LOAD 
 Sales_ID,
 Product_ID
 Price,
 UnitPrice,
 Discount
 Sales_DateFROM Sales.qvd (qvd) 
WHERE EXISTS Sales_Date;Drop Table TMP_DATE_TABLE;
```

如果您不知道您的 QVD 的最大日期，有一种更简单的方法可以找到答案:

```
//Optimized load
TMP_INV_DATE: 
Load INV_DATE 
FROM invoice_data.qvd (qvd);TMP_MAXINV_DATE:
//preceding load
Load Max(INV_DATE) as maxinvdate;
;
Load FieldValue(‘INV_DATE’,IterNo()) as INV_DATE
AutoGenerate(1)
While not Isnull(FieldValue(‘INV_DATE’,IterNo()));Let var_maxinvdate = Peek(‘maxinvdate’,-1,’TMP_MAXINV_DATE’);Drop tables TMP_INV_DATE, TMP_MAXINV_DATE; //not needed
```

我希望你们喜欢这些漂亮的把戏！敬请关注后续文章。