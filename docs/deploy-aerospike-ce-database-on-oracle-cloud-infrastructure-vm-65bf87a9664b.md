# 在 Oracle 云基础架构虚拟机上部署 Aerospike CE 数据库

> 原文：<https://medium.com/oracledevs/deploy-aerospike-ce-database-on-oracle-cloud-infrastructure-vm-65bf87a9664b?source=collection_archive---------1----------------------->

![](img/1e5b54dc9a15ac0ce990905b0fb2575d.png)

Aerospike 是一个分布式实时 NoSQL 数据库，可用于 web 级应用。Aerospike 是一个无模式模型的键值存储。数据模型包含一个名称空间(相当于一个 RDBMS *数据库*，名称空间内是集合(RDBMS 中的*表*)，集合包含记录(RDBMS 中的*行*)。每个记录都有一个索引键*和一个或多个*库* (RDBMS *列*)来保存与特定*记录*相关的数据。更多关于 Aerospike 架构的信息可以在[这里](https://docs.aerospike.com/server/architecture/overview)找到。*

在本文中，我们将探讨如何使用 Oracle Linux 7 VM 在 Oracle 云上构建和部署 Aerospike。部署之后，我们将连接到数据库并查询它。

![](img/c0d36c0e3fda512ed5a8b26d797bec8c.png)

Credit : Unsplash

# 先决条件

在继续安装步骤之前，确保遵循此处记录的先决条件[。](https://docs.aerospike.com/server/operations/install/linux/rhel)

# 安装步骤

```
$ ssh -i <private-key> opc@<OCI-VM-IP>#/artifacts/aerospike-server-community/<aerospike_version>/aerospike-server-community-<aerospike_version>-<oraclelinux_version>.tgz$ wget -O aerospike.tgz https://download.aerospike.com/artifacts/aerospike-server-community/5.7.0.17/aerospike-server-community-5.7.0.17-el7.tgz$ tar -xvf aerospike.tgz$ cd aerospike-server-<community_or_enterprise-<aerospike_version>-<oraclelinux_version>sudo ./asinstall
```

Aerospike 使用默认服务器配置进行安装。创建了一个默认的内存中测试命名空间。要更改默认服务器配置，请参考此处的。

塞式气塞的配置文件位于:`/etc/aerospike/aerospike.conf`

# 启动气塞

```
$ sudo systemctl start aerospike$ sudo systemctl status aerospike
```

# 安装气塞式工具

在进行下一步之前，确保遵循此处记录的[工具先决条件。](https://docs.aerospike.com/tools/install/requirements)

```
$ wget -O aerospike-tools.tgz [https://www.aerospike.com/download/tools/latest/artifact/el7](https://www.aerospike.com/download/tools/latest/artifact/el7)
# For OL 8, replace “el7” with “el8”.$ tar -xvf aerospike-tools.tgz$ cd aerospike-tools-<version>.el7
# Replace <version> with the version number.
# For OL 8, replace “el7” with “el8”.$ sudo ./asinstall 
$ asadm --version 
$ aql --version 
$ asinfo --version 
$ asbackup --version 
$ asrestore --version
```

# 从 Aerospike 连接和查询数据

连接到 Aerospike 数据库(Aerospike 的默认端口是 TCP/3000)

```
aql -h localhostaql> show namespaces
+ — — — — — — +
| namespaces |
+ — — — — — — +
| “test” |
| “bar” |
+ — — — — — — +
[127.0.0.1:3000] 2 rows in set (0.002 secs)
```

对示例名称空间执行基本操作。

```
 INSERT INTO test.demo (PK, foo, bar) VALUES (‘key1’, 123, ‘abc’)
 INSERT INTO test.demo (PK, foo, bar) VALUES (‘key1’, CAST(‘123’ AS INT), JSON(‘{“a”: 1.2, “b”: [1, 2, 3]}’))
 INSERT INTO test.demo (PK, foo, bar) VALUES (‘key1’, LIST(‘[1, 2, 3]’), MAP(‘{“a”: 1, “b”: 2}’))
 INSERT INTO test.demo (PK, gj) VALUES (‘key1’, GEOJSON(‘{“type”: “Point”, “coordinates”: [123.4, -56.7]}’))

 SELECT * FROM test.demo 
 DELETE FROM test.demo WHERE PK = ‘key1’
```

## 结论

我们可以看到在 Oracle Linux 7 上安装 Aerospike 并连接和查询数据库是多么容易。在未来的博客中，我们将探索向 Aerospike 添加 SSD 并创建多节点集群。

准备好了解更多信息了吗？请访问 oracle.com/au/cloud/free/了解更多信息。

也可以[访问我们的公懈讨论](https://bit.ly/devrel_slack)！