# 名称:开发人员会喜欢的 Oracle Database 12c 第 2 版第 2 部分的 12 件事

> 原文：<https://medium.com/oracledevs/loooooooooooooooong-names-12-things-developers-will-love-about-oracle-database-12c-release-2-part-b5f6e79698?source=collection_archive---------7----------------------->

![](img/75389b4e3033eb07ea034b8368474659.png)

Oracle 数据库为您处理高速缓存失效。所以作为一个开发者，你不必担心这个。

但是在给事物命名的时候，我们把它变得比应该的更难了。

为什么？

举以下例子:

```
alter table customer_addresses add constraint 
  customer_addresses_customer_id_fk foreign key ( customer_id ) 
  references customers ( customer_id );
```

看起来像标准的外键创建，对吗？

但是有一个问题。运行它，您将获得:

```
SQL Error: ORA-00972: identifier is too long
```

啊啊啊！约束名有点太长了。

保持在 30 字节的限制内是很棘手的。特别是如果你有你必须遵循的命名标准。因此，许多人要求我们允许更长的名字。

从 12.2 开始，我们增加了这个限制。现在最大值是 128 字节。所以现在你可以创建这样的对象:

```
create table with_a_really_really_really_really_really_long_name (
  and_lots_and_lots_and_lots_and_lots_and_lots_of int,
  really_really_really_really_really_long_columns int 
);
```

记住:限制是 128 *字节*。不是人物。所以如果你使用多字节字符集，你会发现你不能创建:

```
create table tablééééééééééééééééééééééééééééééééééééééééééééééééééééééééééééééé
( is_67_chars_but_130_bytes int );
```

这是因为é在 UTF8 等字符集中使用两个字节。所以即使上面的字符串只有 67 个字符，也需要 130 个字节！

【blogs.oracle.com】全文原载于 2016 年 11 月 10 日[](https://blogs.oracle.com/sql/12-things-developers-will-love-about-oracle-database-12c-release-2)**。**