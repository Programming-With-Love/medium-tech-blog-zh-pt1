# Python-oracledb 1.2 已经发布

> 原文：<https://medium.com/oracledevs/python-oracledb-1-2-has-been-released-c1d96deb27c5?source=collection_archive---------0----------------------->

![](img/fc2dd1d7214d914fd0fd8490e4588659.png)

Photo by [Markus Spiske](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

[**python-Oracle db 1.2**](https://oracle.github.io/python-oracledb/index.html)**非常流行的针对 Python 的 Oracle 数据库接口，现在已经在 PyPI 上了。**

python-oracledb 是 python 数据库 API 规范的开源包，增加了许多支持高级 Oracle 数据库特性的功能。这是 cx_Oracle 驱动程序的新名称。

另一个受欢迎的补丁版本已经发布，增加了以前仅在传统“胖”模式下可用的功能(使用 Oracle 客户端库)。还进行了其他改进和错误修复。

# 主要变化

*   为新的 Python 3.11 版本添加了二进制包。
*   支持在瘦模式下绑定和获取类型为`oracledb.DB_TYPE_OBJECT`的数据。这是阻止人们从密集模式迁移的最重要的请求之一。
    您可以使用这样的例子来展示 Oracle 数据库的空间功能:[Spatial _ to _ geo pandas . py](https://github.com/oracle/python-oracledb/blob/main/samples/spatial_to_geopandas.py)。
*   瘦模式现在支持以字符串形式获取`SYS.XMLTYPE`数据。与厚模式不同，获取更长的值不需要转换查询来返回带有`XMLTYPE.GETCLOBVAL()`的 LOB。
    有关示例，请参见文档:[使用 XMLTYPE 数据](https://python-oracledb.readthedocs.io/en/latest/user_guide/xml_data_type.html)
*   应用户要求，瘦模式中增加了一些连接字符串语法和钱包增强功能。这增加了可以从密集模式迁移的环境数量。

我要提到的另一个变化是一个新的、可运行的例子 [load_csv.py](https://github.com/oracle/python-oracledb/blob/main/samples/load_csv.py) 显示从 csv 文件加载。该示例展示了如何使用`batcherrors`标志过滤掉“有噪声”的数据，同时仍然插入有效的行。从 CSV 加载是一个常见问题，所以很高兴有这个例子来补充我们关于这个主题的[文档](https://python-oracledb.readthedocs.io/en/latest/user_guide/batch_statement.html)。

参见[发布说明](https://python-oracledb.readthedocs.io/en/latest/release_notes.html)获得更多改进和错误修复。

最后，你想要一些互动的东西吗？查看我们的“LiveLabs”介绍 Python-Oracle db:[Python 和 Oracle 数据库入门](https://apexapps.oracle.com/pls/apex/r/dbpm/livelabs/view-workshop?wid=3482)。

# 安装或升级 python-oracledb

您可以通过运行以下命令来安装或升级 python-oracledb:

```
python -m pip install oracledb --upgrade
```

pip 选项—代理和—用户在某些环境中可能有用。详见 [python-oracledb 安装](https://python-oracledb.readthedocs.io/en/latest/user_guide/installation.html)。

# python-oracledb 参考

首页:【oracle.github.io/python-oracledb/index.html 

安装说明:[python-oracledb.readthedocs.io/en/latest/installation.html](https://python-oracledb.readthedocs.io/en/latest/user_guide/installation.html)

文档:[python-oracledb.readthedocs.io/en/latest/index.html](https://python-oracledb.readthedocs.io/en/latest/index.html)

发行说明:[python-oracledb.readthedocs.io/en/latest/release_notes.html](https://python-oracledb.readthedocs.io/en/latest/release_notes.html)

讨论:[github.com/oracle/python-oracledb/discussions](https://github.com/oracle/python-oracledb/discussions)

问题:[github.com/oracle/python-oracledb/issues](https://github.com/oracle/python-oracledb/issues)

源代码库:[github.com/oracle/python-oracledb](https://github.com/oracle/python-oracledb)

如果你对 Oracle 开发人员在他们的自然环境中发生的事情感到好奇，请加入我们的公共休闲频道！