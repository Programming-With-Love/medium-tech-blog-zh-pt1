# 单语句表分区:开发人员会喜欢的 Oracle Database 12c 第 2 版第 9 部分的 12 件事

> 原文：<https://medium.com/oracledevs/single-statement-table-partitioning-12-things-developers-will-love-about-oracle-database-12c-7200f5df30bf?source=collection_archive---------1----------------------->

这是一个经常出现在[问汤姆](https://asktom.oracle.com/pls/apex/f?p=100:1:0)的问题:

"如何将非分区表转换为分区表？"

在 12.2 之前，这是一个复杂的过程。您必须创建一个表的分区副本并传输数据。

您可以使用 DBMS_redefinition 在线完成这项工作。但这是一个令人头痛的问题，很容易出错。

在 Oracle Database 12c 第 2 版中，这很容易。您所需要的只是一个 alter table 命令:

```
create table t ( x int, y int, z int ); alter table t modify 
  partition by range (x) interval (100) (   
    partition p1 values less than (100) 
  ) online;
```

你完了！

"但是所有的索引呢？"我听到你哭泣。嗯，你也可以转换它们！

只需添加一个“更新索引”子句，并声明在转换后希望它们是本地的还是全局的:

```
create index iy on t (y); 
create index iz on t (z); alter table t modify 
  partition by range (x) interval (100) (
    partition p1 values less than (100) 
  ) update indexes ( 
    iy local, iz global 
  );
```

如果你真的愿意，你可以给你的全局索引不同的分区方案。

虽然可以从非分区表更改为分区表，但是不能再返回。你也不能改变分区方案，例如从列表到范围。尝试这样做，你会得到:

```
ORA-14427: table does not support modification to a partitioned state DDL
```

但是如果你想让*变得真正*奇特，你也可以直接从一个普通的桌子到一个有子分区的桌子！

```
alter table t modify 
  partition by range (x) interval (100) subpartition by hash (y)   
  subpartitions 4 ( 
    partition p1 values less than (100) 
  ) online;
```

【blogs.oracle.com】全文原载于 2016 年 11 月 10 日[](https://blogs.oracle.com/sql/12-things-developers-will-love-about-oracle-database-12c-release-2)**。**