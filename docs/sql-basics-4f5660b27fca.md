# SQL 基础知识

> 原文：<https://medium.com/edureka/sql-basics-4f5660b27fca?source=collection_archive---------2----------------------->

![](img/c4c7e407253cfab4f624557c90672607.png)

在当今世界，数据就是一切。但是要管理它，必须掌握数据管理的艺术。随之而来的是语言，即 **SQL** ，它是一切的基础。SQL 是大多数公司使用的关系型数据库的核心。通过这篇文章，我将帮助您开始学习 SQL 基础知识。

本文将涵盖以下主题:

*   SQL 简介
*   数据和数据库
*   如何创建数据库
*   删除数据库
*   桌子
*   创建表格
*   放下桌子
*   截断表格
*   SQL 基本查询
*   挑选
*   在哪里
*   和，或者，不是
*   插入
*   聚合函数
*   分组依据，拥有，排序依据
*   空
*   更新和删除
*   运营商内部和之间
*   SQL 中的别名

我们将逐一介绍这些类别，让我们开始吧。

**SQL 简介**

![](img/f857a9aedfaff0e94eda7ae61f238b76.png)

SQL 是在 20 世纪 70 年代早期由 IBM 的唐纳德·d·钱伯林和雷蒙德·f·博伊斯开发的。这最初叫做**续集** ( **S** 结构化 **E** 英语**查询**L 语言)。SQL 的主要目标是更新、存储、操作和检索存储在关系数据库中的数据。多年来，SQL 经历了许多变化。添加了许多功能，如支持 XML、触发器、存储过程、正则表达式匹配、递归查询、标准化序列等等。

***那么，SQL 和 MySQL 有什么不同呢？***

![](img/630503836c7155a18a865ce410c04d4c.png)

关于这个话题有一个误解或混乱，我想在这里澄清一下。

SQL 是一种标准语言，用于以查询的形式对数据库进行操作。但是 MySQL 是开源的数据库管理系统或者简单的说是一个数据库软件。MySQL 将组织这些数据，然后存储在它的数据库中。

## 优势:

*   SQL 有定义良好的标准
*   SQL 本质上是交互式的
*   在 SQL 的帮助下，人们可以创建*多个视图*
*   SQL 中代码的可移植性是一个突出的特性

# 数据和数据库

首先，我们需要理解什么是数据。数据是关于感兴趣的对象的事实的集合。关于学生的数据可能包括姓名、唯一 id、年龄、地址、教育程度等信息。该软件必须存储数据，因为它需要回答一个问题，例如，有多少学生是 15 岁？

# 数据库:

数据库是一个有组织的数据集合，通常通过计算机系统以电子方式存储和访问。简而言之，我们可以说数据库是存储数据的地方。最好的类比就是图书馆。图书馆包含了大量不同类型的书籍，在这里，图书馆是一个数据库，书籍是数据。

数据库可以大致分为以下几组:

*   集中式数据库
*   分布式数据库
*   NoSQL 数据库
*   操作数据库
*   关系数据库
*   云数据库
*   面向对象的数据库
*   图形数据库

现在我们将更多地关注使用 SQL 进行操作的关系数据库。让我们使用一些

**如何创建数据库？**

我们使用 CREATE DATABASE 语句创建一个新的数据库。

语法:

`CREATE DATABASE databasename;`

示例 **:**

`CREATE DATABASE School;`

因此，将创建名称学校的数据库。如果要删除这个数据库，必须使用以下语法。

**如何删除数据库？**

语法:

`DROP DATABASE databasename;`

示例:

`DROP DATABASE School;`

名为 School 的数据库将被删除。

# 桌子

数据库中的表只不过是表格形式的数据集合。它由**列**和**行**组成。该表包含使用垂直列和水平行模型的数据元素，也称为值。一行和一列的交叉点称为**一个单元**。一个表可以有任意数量的行，但应该有指定数量的列。

![](img/d9ebe2701122ad2d226ec60c81f0c79b.png)

**创建表格**

因此，为了在数据库中创建一个表，我们使用下面的 SQL 查询。

语法:

`CREATE DATABASE databasename;`

示例 **:**

`CREATE DATABASE School;`

因此，将创建名称学校的数据库。如果要删除这个数据库，必须使用以下语法。

如何删除数据库？

语法:

`DROP DATABASE databasename;`

示例:

`DROP DATABASE School;`

名为 School 的数据库将被删除。

# 桌子

数据库中的表只不过是表格形式的数据集合。它由**列**和**行**组成。该表包含使用垂直列和水平行模型的数据元素，也称为值。一行和一列的交叉点称为**一个单元**。一个表可以有任意数量的行，但应该有指定数量的列。

![](img/20539a50d2caf023628555c6bf207e59.png)

**创建表格**

因此，为了在数据库中创建一个表，我们使用下面的 SQL 查询。

句法

```
CREATE TABLE table_name (column1 datatype,column2 datatype,column3 datatype, ....);
```

这里，关键字 Create Table 用于告诉数据库我们将创建一个新表。然后我们需要提到表名。该名称必须是唯一的。SQL 不区分大小写，但是存储在表中的数据将区分大小写。我们在左括号和右括号内添加列。我们用特定的数据类型指定每一列。

示例:

```
CREATE TABLE Student (
studentID int,
FName varchar(25),
LName varchar(25),
Address varchar(50),
City varchar(15),
Marks int);
```

我们创建了一个名为 Student 的表，并在表中添加了一些参数。这就是我们如何使用 SQL 创建一个表。

**降表**

如果我们想删除整个表及其所有数据，那么我们必须使用 DROP 命令。

语法:

`DROP TABLE table_name;`

示例:

`DROP TABLE Student;`

所以学生表会被删除。

**截断表格**

如果我们只想删除表中的数据，而不是表本身，该怎么办？那么我们必须使用截断查询。

语法:

`TRUNCATE TABLE table_name;`

示例:

`TRUNCATE TABLE Student;`

当我们执行上述查询时，表中的数据将被删除，但表仍然存在。要了解更多信息，您可以查看这篇关于修改表的文章。

我们可以借助名为 ***SQL 约束*** 的概念，提高通过表格进入数据库的数据的准确性和可靠性。这些约束确保在数据事务方面没有违规，如果发现违规，则动作将被终止。约束的主要用途是限制可以放入表中的数据类型。由于本文与 SQL 基础知识相关，我将只讨论最常用的约束。

*   *默认值* —如果没有指定值，则添加一组列的默认值
*   *NOT NULL* —这确保空值不会存储在列中
*   *唯一* —如果应用该约束，输入到表格中的值将是唯一的
*   *索引* —用于从数据库中创建和检索数据
*   *主键* —它是被选择来唯一标识关系中的元组的候选键。
*   *外键* —外键是子表中一个或多个列的集合，其值需要与父表中的相应列相匹配
*   *CHECK* —如果我们想要满足列中的特定条件，那么我们使用 CHECK 约束

# SQL 基本查询

现在，让我们关注一些 SQL 基本命令，这些命令是开始学习 SQL 时应该知道的。有许多看似基本的查询，但我已经涵盖了对初学者来说非常重要的几个。为了解释所有的查询，我考虑了学生表，我将使用它。

**选择**

这是可以用来操作数据库的最基本的 SQL 查询。select 命令用于从数据库中选择数据并显示给用户。

*语法*:

```
Select column 1, column 2…..column N
From Table;
```

*示例*:

`Select name From Student;`

上面的例子将显示学生表中的所有名字。如果我们想显示表中的所有字段，那么我们必须使用*(星号)运算符。这将显示整个表格。

*举例*:

`Select * from Student;`

如果我们想显示某个字段而没有任何重复，那么我们使用 DISTINCT 关键字和 select 命令。

*举例*:

`Select DISTINCT FName From Student;`

**哪里**

如果我们只需要表中的某些记录，那么我们使用 where 子句。Where 子句充当过滤机制。在 Where 部分下，我们需要指定某些条件，只有满足这些条件，才会提取记录。

*语法*:

```
SELECT column1, column2, ...column N
FROM table_name
WHERE condition;
```

*示例*:

```
SELECT FName FROM Students
WHERE City='Delhi';
```

**与，或，非**

如果我们需要在 where 子句中添加两个或更多条件，那么我们可以使用上面提到的操作符。这些关键字会增加查询的复杂性。

*   AND 运算符:如果由 AND 分隔的所有条件都为真，该运算符将显示一条记录。

*语法*:

```
SELECT column1, column2, ...
FROM table_name
WHERE condition1 AND condition2 AND condition3 ...;
```

*示例*:

```
SELECT * FROM Student
WHERE FName='John' AND Lname='Doe';
```

*   or 运算符:如果由 OR 分隔的任何条件为真，该运算符将显示一条记录。

*语法*:

```
SELECT column1, column2, ...
FROM table_name
WHERE condition1 OR condition2 OR condition3 ...;
```

*示例*:

```
SELECT * FROM Student
WHERE FName='John' OR Lname='Doe';
```

*   NOT 运算符:如果条件不为真，该运算符将显示一条记录。

*语法*:

```
SELECT column1, column2, ...
FROM table_name
WHERE NOT condition;
```

*例子*:

```
SELECT * FROM Student
WHERE NOT Lname='Doe';
```

**插入**

如果我们想向表中插入任何新的记录或数据，那么我们可以使用 insert 查询。我们可以用两种方式使用 Insert into:

*   这里我们指定需要插入记录的列名。

*语法*:

```
INSERT INTO table_name (column1, column2,...)
VALUES (value1, value2, value3, ...);
```

*示例*:

```
Insert into Student(studentID, FName, LName, Address, City, Marks)
Values (101, 'JHON','DOE','#21, MG ROAD',  'Bengaluru', 550);
```

*   在这种情况下，我们不必指定表的列。但是要确保值的顺序与表中列的顺序相同。

*语法*:

```
INSERT INTO table_name
VALUES (value1, value2, value3, ...);
```

*示例*:

`INSERT INTO Student VALUES (102, ‘Alex’,’Cook’,’#63, Brigade ROAD, NEAR HAL’, ‘Bengaluru’, 490);`

如果我们想插入特定的列，那么我们需要遵循下面的方法。

*示例*:

`INSERT INTO Student(studentID, FName) VALUES (103, ‘Mike’);`

**聚合函数**

聚合函数是这样一种函数，其中多行的值根据特定条件作为输入分组在一起，并返回单个值。我们经常在 SELECT 语句的 GROUP BY 和 HAVING 子句中使用聚合函数。我们将在本节的后面讨论 GROUP BY、ORDER BY 和 HAVING。一些聚合函数包括计数、求和、AVG、最小值、最大值。

让我们逐一讨论。

*   COUNT():该函数返回符合指定标准的行数。

*语法*:

```
SELECT COUNT(column_name)
FROM table_name
WHERE condition;
```

*示例*:

```
SELECT COUNT (studentID)
FROM Student;
```

*   AVG():这个函数返回一个数值列的平均值。

*语法*:

```
SELECT AVG(column_name)
FROM table_name
WHERE condition;
```

*举例*:

```
SELECT AVG(Marks)
FROM Student;
```

*   SUM():这个函数返回一个数字列的总和。

*语法*:

```
SELECT SUM(column_name)
FROM table_name
WHERE condition;
```

*举例*:

```
SELECT SUM(Marks)
FROM Student;
```

*   MIN():该函数返回所选列的最小值。

*语法*:

```
SELECT MIN(column_name)
FROM table_name
WHERE condition;
```

*示例*:

```
SELECT MIN(Marks) AS LeastMarks
FROM Student;
```

*   MAX():该函数返回所选列的最大值。

*语法*:

```
SELECT MAX(column_name)
FROM table_name
WHERE condition;
```

*示例*:

```
SELECT MAX(Marks) AS HighestMarks
FROM Student;
```

注意:我们在这里使用了别名(如 new_name ),稍后我们将对此进行讨论。

**分组依据，拥有，排序依据**

这些关键字(GROUP BY、HAVING、ORDER BY)在查询中用于增加功能。他们每个人都有特定的角色要扮演。

*   分组依据:此功能用于将相似类型的数据排列到一个组中。例如，如果表中的列由不同行中的相似数据或值组成，那么我们可以使用 GROUP BY 函数对数据进行分组。

*语法*:

```
SELECT column_name(s)
FROM table_name
WHERE condition
GROUP BY column_name(s);
```

*例子:*

```
SELECT COUNT(StudentID), Fname
FROM Student
GROUP BY Fname;
```

*   HAVING:这个子句用于放置我们需要决定哪个组将成为最终结果集的一部分的条件。此外，我们不能使用聚合函数，如 *SUM()、COUNT()、*等。用 *WHERE* 子句。在这种情况下，我们不得不使用 HAVING 条件。

*语法*:

```
SELECT column_name(s)
FROM table_name
WHERE condition
GROUP BY column_name(s)
HAVING condition;
```

*例如:*

```
SELECT Fname, SUM(Marks)
FROM Student
GROUP BY Fname
HAVING SUM(Marks)>500;
```

*   ORDER BY:该关键字用于按升序或降序对结果集进行排序。 *ORDER BY* 关键字默认对记录进行升序排序。如果我们想按降序排列记录，请使用 DESC 关键字。

```
SELECT column1, column2, ...
FROM table_name
ORDER BY column1, column2, ... ASC|DESC;
```

*示例*:

```
SELECT COUNT(StudentID), City
FROM Student
GROUP BY City
ORDER BY COUNT(StudentID) DESC;
```

**空值**

在 SQL 中，我们使用空项来表示丢失的值。表中的空值是看起来为空的值。具有空值的字段在 SQL 中是没有值的字段。请注意，空值不同于零值或包含空格的字段。

为了检查空值，我们不应该使用诸如、=等操作符。SQL 中不支持。我们有特殊的关键字，即 IS NULL 和 NOT NULL。

*   **为空** *语法*:

```
SELECT column_names
FROM table_name
WHERE column_name IS NULL;
```

*例子*:

```
Select Fname, Lname
From Student
Where Marks IS NULL;
```

*   **不为空** *语法*:

```
SELECT column_names
FROM table_name
WHERE column_name IS NOT NULL;
```

*示例*:

```
Select Fname, Lname
From Student
Where Marks IS NOT NULL;
```

**更新和删除**

*   UPDATE:UPDATE 命令用于修改表中的行。update 命令可用于同时更新单个字段或多个字段。

*语法*:

```
UPDATE table_name
SET column1 = value1, column2 = value2,...
WHERE condition;
```

*示例*:

```
UPDATE Student
SET Fname = 'Robert', Lname= 'Wills'
WHERE StudentID = 101;
```

*   DELETE:SQL DELETE 命令用于从数据库表中删除不再需要的行。它从表*中删除整行。*

*语法*:

```
DELETE FROM table_name
WHERE condition;
```

*举例*:

```
DELETE FROM Student
WHERE FName='Robert';
```

这里有一个特例，如果我们需要删除整个表记录，那么我们必须指定表名。该特定表的数据将被分割。

*示例*:

`Delete From Student;`

现在出现的一个主要问题是:删除和截断命令之间的区别是什么？答案很简单。DELETE 是一个 DML 命令，而 TRUNCATE 是一个 DDL 命令，同样，DELETE 会逐个删除记录，并在事务日志中为每次删除创建一个条目，而 TRUNCATE 会在事务日志中取消页面分配并创建一个取消页面分配的条目。

**在运营商之间**

*   IN 运算符用于在 WHERE 子句中指定多个值。它是多重或的简称。

*语法*:

```
SELECT column_name(s)
FROM table_name
WHERE column_name IN (value1, value2, ...);
```

*举例*:

```
SELECT StudentID, Fname, Lname
FROM Student
WHERE City IN ('Delhi', 'Goa', 'Pune','Bengaluru');
```

*   BETWEEN 运算符将选择指定范围内的特定值。必须添加开始值和结束值(范围)。

*语法*:

```
SELECT column_name(s)
FROM table_name
WHERE column_name BETWEEN value1 AND value2;
```

*示例:*

```
SELECT StudentID, Fname, Lname FROM Student
WHERE Marks BETWEEN 400 AND 500;
```

**SQL 中的别名**

别名是为表或列提供临时名称的过程，以便在查询复杂时有所帮助。它增加了查询的可读性。这种重命名是临时的，原始数据库中的表名不会改变。我们可以给列或表起别名。下面我提到了这两种语法。

列别名的*语法**:*

```
SELECT column_name AS alias_name
FROM table_name;
```

*列别名*的示例*:*

```
SELECT CustomerID AS ID, CustomerName AS Customer
FROM Customers;
```

*表别名的语法**:*

`SELECT column_name(s) FROM table_name AS alias_name;`

*表格别名*示例*:*

```
SELECT S.Fname, S.LName
FROM Student as S
```

这就把我们带到了这篇 SQL 基础文章的结尾。我希望您理解了 SQL 基础知识的概念。

如果你想查看更多关于人工智能、DevOps、道德黑客等市场最热门技术的文章，那么你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/sql-basics/)

请留意本系列中解释 SQL 其他各方面的其他文章。

> *1。*[*SQL For Data Science*](/edureka/sql-for-data-science-a8fe10fe2ef9)
> 
> *2。* [*甲骨文面试前 50 题*](/edureka/oracle-interview-and-answers-d1a99534e2d0)
> 
> [3*。前 65 名 SQL 面试问题*](/edureka/sql-interview-questions-162f97f37ac2)

*原载于 2019 年 9 月 11 日*[*https://www.edureka.co*](https://www.edureka.co/blog/sql-basics/)*。*