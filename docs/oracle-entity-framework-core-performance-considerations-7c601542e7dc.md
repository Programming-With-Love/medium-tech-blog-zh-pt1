# Oracle 实体框架核心性能注意事项

> 原文：<https://medium.com/oracledevs/oracle-entity-framework-core-performance-considerations-7c601542e7dc?source=collection_archive---------1----------------------->

![](img/cc55e9eb0372f37d871eeb534c5518bd.png)

一些开发人员报告了从 2.x 升级到 Oracle EF Core 3.1 后运行时性能下降的情况。NET 字符串实体属性绑定数据类型的 Unicode 支持与数据库列数据类型的不匹配。

当映射设置正确时，应用程序将字符串实体属性值绑定为数据库`NVARCHAR2`列的`NVARCHAR2`，或者绑定为数据库`VARCHAR2`列的`VARCHAR2`。如果类型不匹配，服务器端会在运行时进行额外的处理来执行数据类型转换，从而降低性能。当有大量值需要转换时，性能会显著下降。这种不匹配会导致数据库的执行计划更改为完全扫描。

要避免这种数据类型不匹配的性能问题，请执行下列操作之一:

*   如果数据库表已经存在，使用脚手架来生成对应于关系数据库表的实体类。这样做可以为每个表列生成正确的 fluent API。
*   如果实体类存在，但没有相应的数据库表，则使用迁移为字符串实体属性生成相应的数据库列，这将遵循调用的 IsUnicode()或 has columntype()fluent API。
*   如果手动创建对应于数据库表的实体类，使用适当的 IsUnicode()或 HasColumnType() fluent API 将每个字符串实体属性正确映射到`NVARCHAR2`或`VARCHAR2`列类型，以避免不匹配。

简单来说，让 EF Core 自动生成表(迁移)或类(脚手架)，那么你就不会碰到这个问题。如果手动修改数据类型映射，请注意映射要精确。如果您看到明显的性能问题，请再次检查您的映射。

映射 Oracle EF 核心数据类型时需要注意的其他要点:

*   如果同时使用 IsUnicode()和 HasColumnType() fluent API，则 has columntype()fluent API 优先。
*   对于迁移。默认情况下，NET String 实体属性映射到`NVARCHAR2`。对于脚手架，`VARCHAR2`和`NVARCHAR2`列都映射到。网串。
*   如果数据库列类型是`VARCHAR2`，那么应该使用 IsUnicode(false)或 has columntype(" varchar 2(<length>))")fluent API 将字符串实体属性正确映射到`VARCHAR2`列。这避免了性能下降的问题。
*   如果一个字符串实体属性与一个`NVARCHAR2`列相关联，那么既不需要调用 IsUnicode()也不需要调用 has columntype()fluent API。或者，可以调用 IsUncode(true)或 has columntype(" nvarchar 2(<length>)")fluent API 将数据绑定为`NVARCHAR2`。
*   在甲骨文。EntityFrameworkCore 2.19.70 及更早版本中，在执行 LINQ 查询时，字符串实体属性值总是被绑定为`VARCHAR2`。从 Oracle EF Core 2.19.80 开始，这种行为发生了变化。字符串实体属性值现在基于为实体字符串属性指定的映射进行绑定。在 Oracle EF Core 2.19.70 下性能最佳的应用程序在升级到更高版本的 Oracle EF Core 时性能可能会下降。
*   如果在 Oracle EF 核心升级后遇到新的性能问题，请验证与`VARCHAR2`列相关联的字符串实体属性既没有设置 IsUnicode(false)，也没有设置 has columntype(" varchar 2(<length>))")流畅 API，也没有设置等效的数据注释。如果是这样，添加这些 fluent API 之一，以便使用正确的类型绑定基于字符的数据。

有了这个技巧，您可以避免 Oracle EF 核心应用程序遇到这种性能障碍。