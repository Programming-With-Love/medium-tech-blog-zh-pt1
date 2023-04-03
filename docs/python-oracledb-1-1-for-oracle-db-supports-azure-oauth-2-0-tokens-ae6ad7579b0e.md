# python-oracledb 1.1 for Oracle DB 支持 Azure OAuth 2.0 令牌

> 原文：<https://medium.com/oracledevs/python-oracledb-1-1-for-oracle-db-supports-azure-oauth-2-0-tokens-ae6ad7579b0e?source=collection_archive---------0----------------------->

![](img/8549a739bbf7e7db517a86d46dec8f6b.png)

Photo by [Ricardo Gomez Angel](https://unsplash.com/@rgaleriacom?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

[**python-Oracle db**](https://oracle.github.io/python-oracledb/index.html)**1.1，针对 Python 的极为流行的 Oracle 数据库接口，现已量产于** [**PyPI**](https://pypi.python.org/pypi/cx_Oracle/) **。**

python-oracledb 是 python 数据库 API 规范的开源包，增加了许多支持高级 Oracle 数据库特性的功能。

# 主要变化

*   支持 Azure Active Directory OAuth 2.0 和 Oracle Cloud
    基础架构身份和访问管理(IAM)令牌认证到 Oracle 自治数据库。
*   高级队列改进，包括对 JSON 有效负载的支持。
*   厚模式(使用 Oracle 客户端库)现在可以在 Python 的*密码术*包不能用于薄模式连接的系统上使用。请参见[在没有加密包的情况下安装 python-Oracle db](https://python-oracledb.readthedocs.io/en/latest/user_guide/installation.html#installing-python-oracledb-without-the-cryptography-package)。

请参见[发行说明](https://python-oracledb.readthedocs.io/en/latest/release_notes.html)了解更多改进和错误修复。

# 安装或升级 python-oracledb

您可以通过运行以下命令来安装或升级 python-oracledb:

```
python -m pip install oracledb --upgrade
```

pip 选项`--proxy`和`--user`在某些环境下可能有用。详见 [python-oracledb 安装](https://python-oracledb.readthedocs.io/en/latest/user_guide/installation.html)。

# python-oracledb 参考

*   首页:[oracle.github.io/python-oracledb/index.html](https://oracle.github.io/python-oracledb/index.html)
*   安装说明:[python-oracledb.readthedocs.io/en/latest/installation.html](https://python-oracledb.readthedocs.io/en/latest/user_guide/installation.html)
*   文献:[python-oracledb.readthedocs.io/en/latest/index.html](https://python-oracledb.readthedocs.io/en/latest/index.html)
*   发行说明:[python-oracledb.readthedocs.io/en/latest/release_notes.html](https://python-oracledb.readthedocs.io/en/latest/release_notes.html)
*   讨论:[github.com/oracle/python-oracledb/discussions](https://github.com/oracle/python-oracledb/discussions)
*   议题:【github.com/oracle/python-oracledb/issues 
*   源代码库:【github.com/oracle/python-oracledb 

想谈谈吗？在这里加入我们的[公共开发者松弛频道](https://bit.ly/odevrel_slack)。