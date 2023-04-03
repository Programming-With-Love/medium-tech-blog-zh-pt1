# 在 Visual Builder 云服务中从命令行批量导入/导出数据

> 原文：<https://medium.com/oracledevs/bulk-import-export-of-data-from-command-line-in-visual-builder-cloud-service-c51ea3121cc6?source=collection_archive---------2----------------------->

您始终可以通过 web 用户界面从 VBCS 手动导出和导入数据。要导入/导出整个应用程序，请转到业务对象>数据管理器。要导入/导出单个对象，请转到业务对象>数据设计器并打开数据选项卡。

在 9 月 1 日发布的 17.3.5 中，您现在还可以使用我们的 API 通过命令行进行批量导入/导出。如果您希望设置一个每日 cron 作业，将您的自定义业务对象中的数据与另一个表中的数据同步，这将非常方便。

现在，应用程序的任何设计者都可以使用基本的 auth 来访问导入/导出 API，就像查询单个对象和执行单行操作的普通数据 API 一样。

端点如下(用于访问应用程序 MyApp 1.0 版本的开发模式):

**导入整个数据模型:** POST[https://host:port/design/MyApp/1.0/resources/data mgr/Import](https://host:port/design/MyApp/1.0/resources/datamgr/import)
查询参数" filename ":上传文件的名称
主体应该是包含 CSV、xls 和/或 xlsx 文件的 zip 文件，这些文件的文件名(或 Excel 表名)应该与现有的对象 id 相匹配

**针对特定业务对象的导入:** POST[https://host:port/design/MyApp/1.0/resources/data mgr/Import/my Object](https://host:port/design/MyApp/1.0/resources/datamgr/import/myObject)
查询参数“filename”:正在上传的文件的名称
查询参数“append”:boolean，如果为 true，则追加新行，如果为 false(默认值)，则所有现有数据都替换为上传文件的内容。正文应该是 csv、xls 或 xlsx

**导出整个数据模型:** GET[https://host:port/design/MyApp/1.0/resources/data mgr/Export](https://host:port/design/MyApp/1.0/resources/datamgr/export)
流回包含每个实体的 csv 文件的 zip 文件

**导出特定的业务对象:** GET[https://host:port/design/MyApp/1.0/resources/data mgr/Export/myObject](https://host:port/design/MyApp/1.0/resources/datamgr/export/myObject)
为 ID 为“my Object”的自定义业务对象流回一个 csv

对于阶段模式或实时模式，将/design/替换为/deployment/