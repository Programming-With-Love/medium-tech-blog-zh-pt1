# 使用 APEX 22.1 深入了解 MySQL 数据

> 原文：<https://medium.com/oracledevs/get-insight-on-mysql-data-using-apex-22-1-7fe613c76ca5?source=collection_archive---------4----------------------->

![](img/ecb04e22dac3ac3509f87534191f1dbb.png)

甲骨文宣布在**甲骨文数据库工具服务**中支持 **MySQL 连接**。在这一宣布之后，又发布了另一个令人兴奋的功能 **APEX 22.1、**，它可以直接基于甲骨文云基础设施(OCI)中的 **MySQL 数据库服务(MDS)** 构建应用。此外，该功能使用户能够直接从 MDS 执行分析并创建数据的可视化。从技术上讲，APEX 的这一功能是通过利用 ORDS ***“启用 REST 的 SQL 服务”*** 来实现的。

今天，我们将讨论一个使用案例，我们将使用 APEX 可视化和分析 MDS 的数据。为此，我们使用了居住在 MDS 的销售数据。我们将利用与**始终免费自治数据库****(**[https://www.oracle.com/in/cloud/free/](https://www.oracle.com/in/cloud/free/))一起提供的 APEX。

这个用例可以通过 **5 个简单步骤**完成:

***步骤 1:在 OCI 数据库工具服务中创建一个 MySQL 连接***

![](img/717f809379dabe0b2e65ff14a0fe2f3f.png)

我们需要一个**私有端点**和一个**密码秘密**来创建这些连接。在这里，私有端点将帮助我们使用私有网络安全地访问数据。

![](img/f51e2edef622e2caa096b924a8ca96cd.png)

密码秘密将有助于在 **OCI 保险库**中安全地存储和管理用户凭证，并使用主密钥对其进行加密。

![](img/5f236201806fd9241c04e1d932e459ac.png)![](img/cf4bf25e6f3ed73fb77078574821a180.png)![](img/35e30183863f1aa1e466e083c617f017.png)

现在我们准备创建 MySQL 连接。

![](img/b48df4c369e22adc2c59167bec96b370.png)

使用 **SQL 工作表测试连通性。**

![](img/0a8d536c199ac118f44a1b946e30ee98.png)

***第 2 步:在 APEX App Builder 中创建 web 凭证***

从自治数据库登录到 APEX 控制台。

![](img/70a7bddf2b776ca3dfb13be5f51e04d9.png)![](img/fc7b4aaa43a19ca74b470aadc68825a4.png)

登录 APEX。

![](img/d67363af77f3f2b1bf664ad2ac960bad.png)

从应用程序构建器转到工作空间实用程序并创建 Web 凭据。

![](img/6b4f26fbb5ae7eb1840c5502e2388bb3.png)

我们需要提供 OCI 身份验证来创建 web 凭据。请参考[https://docs . Oracle . com/en/cloud/PAAs/management-cloud/logcs/create-credentials-OCI-authentic ation . html](https://docs.oracle.com/en/cloud/paas/management-cloud/logcs/create-credentials-oci-authentication.html)了解更多详细信息。

![](img/06f9a66ab6975a66f877f235606cc029.png)

***第三步:在 APEX App Builder*** 中创建一个“启用 Rest 的 SQL 服务”

![](img/567eac0f42aabe0675e65606aaf6c142.png)![](img/17b8addfaf6da918b8459dddbffce64f.png)

端点 URL 的示例:

ocid 1 . database tools connection . oc1 .<oci region="">。</oci>

![](img/5b7e75b323dd539a8e3aab7884ab1161.png)![](img/02704c3ce86a7e256d4e57de34f248b4.png)

测试“启用 Rest 的 SQL 服务”

![](img/0f99e1a37a1ab19c96bf88c0849860a6.png)

***步骤#4:创建 APEX 应用程序***

![](img/8b8be39e6fa255a9592e811b49792b0b.png)![](img/aafdc94157785d3d44edac72c4797820.png)

在新创建的应用程序中添加图表页面。

![](img/c60cdd93e3026eeb9282586cf92bdd56.png)![](img/6a323c680bc789ac17261c495b99827d.png)

***步骤#5:用 APEX*** 中创建的图表/仪表板分析数据

在这里，我们将绘制各大洲的收入数据。

![](img/d9cb0b03239df21d1e6578d298eb1e49.png)![](img/828bbfe9b6f697ed708aef2c22b6d461.png)

客户数量和洲际收入。

![](img/2816f370754ab4b23daa0d36a87135e0.png)

每日收入。

![](img/ea543e38a261b6dc3c491322e79e92e6.png)

# 结论

这是使用 APEX 平台创建可视化和分析数据的一个很好的例子。目前，这个平台支持 csv 格式的数据、OCI 的 MySQL、Oracle 数据库等等。

展望未来，我希望看到更多的 MDS 用户尝试这一功能。

大家来说说吧！在这里加入[公共松弛频道。](https://bit.ly/devrel_slack)