# 将 SQLAlchemy 2.0 与 python-oracledb 一起用于 Oracle 数据库

> 原文：<https://medium.com/oracledevs/using-the-development-branch-of-sqlalchemy-2-0-with-python-oracledb-d6e89090899c?source=collection_archive---------0----------------------->

![](img/10fd266ab65e24951cca2ae74c49ea3e.png)

更新: **SQLAlchemy 2 已经** [**发布**](https://www.sqlalchemy.org/blog/2023/01/26/sqlalchemy-2.0.0-released/) **！**祝贺开发团队。下面的帖子是在最终发布之前写的，但是已经更新了。

早些时候，我在博客[中讲述了如何在 SQLAlchemy 1.4 中使用 python-oracledb 1.0，并展示了几行额外的代码来将新的驱动程序名称映射到我们的旧名称 cx_Oracle。](https://levelup.gitconnected.com/using-python-oracledb-1-0-with-sqlalchemy-pandas-django-and-flask-5d84e910cb19)

对 python-oracledb 名称空间的本机支持已经[合并](https://github.com/sqlalchemy/sqlalchemy/commit/1961e1321440a1e0500ecd13624837ed088eaceb#diff-d58626ed254b6d1d057fdd34e0aa8b56286406bd0be23ca7b534f9c95c4590dbR9-R41)到 SQLAlchemy 2.0。这里有一个小例子，展示了如何以默认的“瘦”模式(不需要 Oracle 客户端库)或“胖”模式(如果您想要额外的功能)进行连接。所有 SQLAlchemy 功能都以瘦模式传递，因此，如果您希望扩展应用程序(例如，使用 Oracle 高级队列功能)，或者如果您希望使用 Oracle 应用程序连续性的可靠性，则只需要胖模式。

首先，我将 SQLAlchemy 升级到最新版本:

`python -m pip install sqlalchemy --upgrade`

当然，还安装了 python-oracledb:

`python -m pip install oracledb`

接下来，我创建了一个文件`[sa-pydb.py](https://gist.github.com/cjbj/b060bb09adc83f29a1afab1e665d9222)`，如下所示。

最后，我将环境变量`PYTHON_USERNAME`和`PYTHON_PASSWORD`设置为我的数据库凭证。我将`PYTHON_CONNECTSTRING`设置为一个“Easy Connect”字符串“localhost/orclpdb1 ”,它包含主机名和在该主机上运行的数据库服务。

运行程序:

`python sa-pydb.py`

给出查询输出

`python-oracledb thn : 1.0.0`

显示正在使用默认的“瘦”模式。

取消注释其中一行`thick_mode`给出:

`python-oracledb thk : 1.0.0`

注意`+oracledb`的使用，这是 SQLAlchemy 2.0 中新增的。additional 关键字区分 cx_Oracle 和 python-oracledb 的使用。

python-oracledb `[connect()](https://python-oracledb.readthedocs.io/en/latest/api_manual/module.html#oracledb.connect)`函数可以接受许多参数。这些可以用 SQLAlchemy connect_args 传递，例如:

```
engine = create_engine(
    f'oracle+oracledb://{username}:{password}@',
    thick_mode=thick_mode,
    connect_args={
        "host": cp.host,
        "port": cp.port,
        "service_name": cp.service_name
    })
```

或者，对我来说更方便的是:

```
engine = create_engine(
    f'oracle+oracledb://{username}:{password}@',
    thick_mode=thick_mode,
    connect_args={
        "dsn": os.environ.get("PYTHON_CONNECTSTRING")
    })
```

要使用 wallet 在默认瘦模式下连接到 Oracle 自治数据库(如果您尚未切换到无 wallet 单向 TLS)，您可以使用类似以下的内容:

```
username = os.environ.get("PYTHON_USERNAME")
password = os.environ.get("PYTHON_PASSWORD")# directory containing the extracted wallet.zip tnsnames.ora file
config_dir = os.environ.get("PYTHON_CONFIG_DIR")# directory containing the extracted wallet.zip ewallet.pem file
wall_loc = os.environ.get("PYTHON_PEM_DIR")# wallet password created when downloading the wallet
wall_pw = os.environ.get("PYTHON_WALLET_PASSWORD")# connect name from the tnsnames.ora file, like 'myclouddb_low'
cs = os.environ.get("PYTHON_CONNECTSTRING")engine = create_engine(
    f'oracle+oracledb://:@',
    connect_args={
        "user": username,
        "password": password,
        "dsn": cs,
        "config_dir": config_dir,
        "wallet_location": wall_loc,
        "wallet_password": wall_pw,
    })
```

为了从防火墙内部进行测试，我给`connect_args`添加了几个额外的参数:

```
 "https_proxy": "myproxy.oracle.com",
        "https_proxy_port": 80
```

使用代理对于生产系统来说太慢了，但是对于快速访问来说却很方便。

想玩的话这里有更完整的例子:[https://github . com/cjbj/python-Oracle db-demos-2022/blob/main/6 _ sqlalchemy _ example . py](https://github.com/cjbj/python-oracledb-demos-2022/blob/main/6_sqlalchemy_example.py)

在 [DevRel public Slack](https://bit.ly/devrel_slack) 上保持对话！