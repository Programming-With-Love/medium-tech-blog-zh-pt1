# 数据类型长度的 PL/SQL 常量:开发人员喜欢的关于 Oracle Database 12c 第 2 版第 3 部分的 12 件事

> 原文：<https://medium.com/oracledevs/pl-sql-constants-for-data-type-lengths-12-things-developers-will-love-about-oracle-database-12c-cb5f4d7c2d06?source=collection_archive---------4----------------------->

在本系列的第 2 部分中，我们看到您现在可以创建名称长度为 128 字节的对象。我知道你们中的一些人非常渴望开始制作名字长得可笑的桌子。但是在你这么做之前，检查一下你的代码。如果你的开发团队使用草率的编码实践，可能会有一些陷阱等着你…

![](img/adf002582e3b45497b5e914110582820.png)

大多数应用程序至少有一段 PL/SQL 从数据字典中选择。例如:

```
begin select table_name into tab from user_tables where ...
```

因为表名的最大长度一直是 30 个字节*永远是*，一些开发人员开始这样声明变量:

```
declare tab varchar2(30);
```

因为谁需要超过 30 个字符，对吗？

但是正如我们[在第 2 部分](/oracledevs/loooooooooooooooong-names-12-things-developers-will-love-about-oracle-database-12c-release-2-part-b5f6e79698)中看到的，升级到 12.2，极限现在是 128 字节！所以人们创建名字更长的表只是时间问题。最终，该代码将失败，并显示:

```
ORA-06502: PL/SQL: numeric or value error: character string buffer too small
```

那怎么办呢？

如果能动态改变 varchar2 的最大长度就好了。因此，您可以在一个地方增加大小，而不是遍历您的 PL/SQL，更改 varchar2 ( 30 ) -> varchar2 ( 128)。

幸运的是，在 12.2 你可以！

新版本使您能够使用常数声明可变长度。所以你可以创建一个常量包:

```
create or replace package constants as tab_length 
  constant pls_integer := 128; 
end constants; 
/
```

然后在声明变量时使用它:

```
declare tab varchar2( constants.tab_length );
```

现在，如果我们再次增加名字的长度，你只需要做一个改变:常量的值！

注意这些并不是完全动态的。PL/SQL 编译器必须在编译时知道变量 size 的值。这意味着你不能把它建立在查询结果的基础上。用户自定义函数也不可用。因此，要使变量能够保存更长的字符串，需要增加 constants.tab_length 的值，并重新编译代码。

现在您可能会想:对于像对象名这样常见的东西，Oracle 肯定会提供说明它们的最大长度的东西吗？

好消息是:我们有:)

在 DBMS_standard 中你会发现一个新的常量。这包括 ora_max_name_len。顾名思义，它规定了对象名的最大长度。因此，您可以将表名变量声明改为:

```
declare 
  tab varchar2( ora_max_name_len ); 
begin
```

最棒的是你现在可以让你的代码经得起未来的考验！通过使用条件编译，您可以将基于数据字典的变量声明更改为:

```
declare 
  $if DBMS_DB_VERSION.VER_LE_12_1 $then 
    tab varchar2( 30 ); 
  $else 
    tab varchar2( ora_max_name_len ); 
  $end
```

然后当你来升级的时候 tab 变量会自动有更大的限制！

你可能会想，这听起来需要做很多工作。你是对的。

您还可以使您的变量 12.2 现在与类型锚定兼容:

```
declare tab user_tables.table_name%type;
```

无论您使用哪种方法，现在就开始准备代码吧。可能要等很久才能升级。但是你的代码越健壮，你就越容易使用新的特性。

*全文原载于 2016 年 11 月 10 日*[*【blogs.oracle.com】*](https://blogs.oracle.com/sql/12-things-developers-will-love-about-oracle-database-12c-release-2)*。*