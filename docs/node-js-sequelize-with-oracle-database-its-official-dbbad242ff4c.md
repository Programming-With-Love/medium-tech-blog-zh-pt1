# Node.js 与 Oracle 数据库的序列化——这是正式的

> 原文：<https://medium.com/oracledevs/node-js-sequelize-with-oracle-database-its-official-dbbad242ff4c?source=collection_archive---------0----------------------->

Node.js Sequelize ORM 的 Oracle 数据库方言现已推出。

![](img/037961aae5d22651d17953022fc2b2f5.png)

Sequelize

*这是由*[*Nilabja Bhattacharya*](/@nilabja10201992)*撰写的客座博文，他是负责驱动程序和框架的 Oracle 数据库开发团队的成员。*

Sequelize 是一个现代的 TypeScript 和 Node.js ORM，正如它在[网站](https://sequelize.org/)上所说，“可靠的事务支持、关系、急切和延迟加载、读取复制等等。”它是最受欢迎的 Node.js 框架，每周获得近 150 万次下载。Oracle 数据库以市场领先的性能、可伸缩性、可靠性和安全性而闻名，无论是在内部还是在云中。他们一起组成了一个伟大的组合。

您现在可以“正式”使用 Oracle 数据库和 Sequelize 了，因为 Oracle 方言已经合并到主线 Sequelize 代码中。感谢所有早期的代码先驱、Oracle 团队和 Sequelize 团队，感谢他们合并了支持。

这篇博客文章概述了与 Oracle 数据库的 Sequelize 交互。未来的文章将会介绍一个例子。

首先，安装 [Sequelize 6.25](https://www.npmjs.com/package/sequelize) (或更高版本)和 [node-oracledb](https://www.npmjs.com/package/oracledb) 5.5(或更高版本)。我们已经对 Oracle 数据库 19c 和 21c 进行了测试。

## 用 Sequelize 连接到 Oracle 数据库

要连接到数据库，您必须创建一个 Sequelize 实例。这可以通过将 Oracle 数据库连接参数单独传递给 Sequelize 构造函数、传递单个连接 URI 或传递用户名、口令和连接字符串来实现:

```
const { Sequelize } = require('sequelize');// Option 1: Passing a connection URI
const sequelize = new Sequelize(
  'oracle://<username>:<password>@<ip>:<port>/<servicename>');// Option 2: Passing parameters separately
const sequelize = new Sequelize(<servicename>, <username>, <password>, {
  host: <ip>,
  port: <port>,
  dialect: 'oracle'
});// Option 3: Passing connection string, username, password
const sequelize = new Sequelize({
  dialect: 'oracle',
  username: <username>,
  password: <password>,
  dialectOptions: {connectString: <connection string>}});
```

当您想要使用 [Oracle Net 别名](https://oracle.github.io/node-oracledb/doc/api.html#tnsnames)或 [Easy Connect Plus](https://oracle.github.io/node-oracledb/doc/api.html#easyconnect) 语法进行连接时，使用最后一种方式会很方便。它还允许将其他选项传递给节点-oracledb 驱动程序[连接 API](https://oracle.github.io/node-oracledb/doc/api.html#getconnectiondb) 。

Oracle 方言文档中的 Sequelize 在这里是。

## 连接池

Sequelize 带有一个通用的连接池，而不是公开数据库供应商的连接池实现。这确实意味着您无法获得内置于 node-oracledb 连接池中的一些高可用性特性。

Sequelize 的连接池可以通过在创建实例时提供池选项来设置，例如:

```
const sequelize = new Sequelize({
  dialect: 'oracle',
  username: <username>,
  password: <password>,
  dialectOptions: {connectString: <connection string>},
  pool: {
    max: 5,
    min: 0,
    acquire: 30000,
    idle: 10000
}});
```

## 在序列中定义模型

Sequelize 中的模型代表一个数据库表，它告诉我们关于它所代表的实体的一些事情，比如表的名称、它所包含的列以及它们的数据类型。模型是 Sequelize 的主要特性之一，它将数据库表抽象成 JavaScript 对象。

```
// This defines a model for 'person'.
// The keys in the parameter represent the database column names
// and the values set the column configurations.
// This example creates the database table PERSON with
// columns ID, NAME, AGE, and HEIGHT.person_object = sequelize.define('person', {
  ID: {
    type: DataTypes.INTEGER,  // maps to INTEGER
    autoIncrement: true,      // automatically increment the value
    primaryKey: true,         // this is the table's primary key
    allowNull: false,         // value cannot be null
  },
  NAME: {
    type: DataTypes.STRING,   // maps to VARCHAR2
    allowNull: false,         // value cannot be null
  },
  AGE: {
    type: DataTypes.INTEGER,  // maps to INTEGER
    allowNull: true,          // value can be null
  },
  HEIGHT: {
    type: DataTypes.DECIMAL,  // maps to NUMBER
    allowNull: true,          // value can be null
  }
});
```

更多信息请参考[https://sequelize . org/docs/V6/core-concepts/model-basics/# using-sequelize define](https://sequelize.org/docs/v6/core-concepts/model-basics/#using-sequelizedefine)

Oracle 方言的数据类型映射可以在[https://github . com/sequelize/sequelize/blob/V6/src/dialects/Oracle/data-types . js](https://github.com/sequelize/sequelize/blob/v6/src/dialects/oracle/data-types.js)中找到

## 模型同步

模型可以通过调用`[model.sync(options)](https://sequelize.org/master/class/src/model.js~Model.html#static-method-sync)`与数据库同步。这通过执行 SQL 语句对数据库进行更改。

*   `User.sync()` -如果不存在，则创建一个表(如果已经存在，则不做任何事情)
*   `User.sync({ force: true })` -这将创建一个表格，如果它已经存在，首先删除它
*   `User.sync({ alter: true })` -检查数据库中一个表的当前状态(它有哪些列，它们的数据类型是什么，等等)，然后在表中执行必要的更改，使它与模型相匹配。

一个例子是:

```
// Refresh the database to the Sequelize models defined
await sequelize.sync({force: true});
```

有关更多信息，请参考[https://sequel ize . org/docs/V6/core-concepts/model-basics/# model-synchron ization](https://sequelize.org/docs/v6/core-concepts/model-basics/#model-synchronization)

## 使用 Sequelize 在 Oracle 数据库表中插入多行

在 Sequelize 中，您可以使用`[Model.bulkCreate()](https://sequelize.org/api/v6/class/src/model.js~model#static-method-bulkCreate)`方法通过一条 SQL 语句有效地创建多条记录。Oracle 方言将`bulkCreate()`映射到 node-oracledb 的`[executeMany()](https://oracle.github.io/node-oracledb/doc/api.html#batchexecution)`函数来获得这种效率。

```
// Sequelize way to insert multiple rows to the table.
// The build() function is a helper to read the data from 
// a CSV file into the format expected by Sequelize
await census.bulkCreate(build(cols, rows));
```

更多信息请参考[https://sequel ize . org/docs/V6/core-concepts/model-query-basics/#批量创建](https://sequelize.org/docs/v6/core-concepts/model-querying-basics/#creating-in-bulk)

## 使用 Sequelize 查询 Oracle 数据库

您可以使用 Sequelize 的`[findAll()](https://sequelize.org/api/v6/class/src/model.js~model#static-method-findAll)`方法来查询数据库表。它支持各种选项。下面的查询示例通过将结果限制在离所提供的偏移量 10 行的范围内来进行分页。

```
// Query the database for 10 rows starting from the offset
const rows = await census.findAll({
  order: ['id'],
  limit: 10,
  offset: (currentPage - 1) * 10
});
```

## 结论

我们很高兴 Oracle 方言现在包含在 Sequelize 中。让我们知道你建造的所有伟大的东西。

## 资源

*   [Node.js](https://nodejs.org/en/)
*   [Sequelize.org](https://sequelize.org/)
*   [用于 Oracle 数据库的节点 oracledb 驱动程序](https://oracle.github.io/node-oracledb/)

# 加入对话！

如果你对甲骨文开发人员在他们的自然栖息地发生的事情感到好奇，请加入我们的公共休闲频道！我们不介意成为你的鱼缸🐠