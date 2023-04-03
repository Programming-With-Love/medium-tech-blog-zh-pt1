# Oracle 云中的数据流处理——简介

> 原文：<https://medium.com/oracledevs/data-stream-processing-in-oracle-cloud-an-introduction-59408bcb442?source=collection_archive---------0----------------------->

![](img/8e6dc5b3ca3317eed43c9ce2f89dc4b6.png)

随着公司希望近乎实时地处理和分析数据，并向用户提供相关的商业见解，数据流正日益流行。

什么是数据流？这是从各种来源生成的连续数据流。可以使用各种流处理技术来存储、分析和处理数据。

数据流的一些常见使用案例包括:

1.  信息发送
2.  操作遥测
3.  网络点击流数据
4.  应用程序日志等。

上述用例可以使用 Oracle Cloud 中的各种服务来开发。

**Oracle 云基础设施(OCI)流服务**为实时接收和使用大容量数据流提供全面管理、可扩展的持久解决方案，并符合企业级安全性。

让我们以一个天气应用的开始来考察一个简单的流媒体用例。

*在 OCI 流媒体服务中摄取天气 API 数据。数据将存储在 JSON 数据库中，可以进一步用于分析用例。*

**实现步骤:**

*   调用由第三方源管理的天气 API
*   使用 python SDK 在 OCI 流服务中将消息发布为数据流
*   每当在流中发布数据时，使用 OCI 服务连接器来调用 OCI 函数。
*   使用 OCI 函数转换数据
*   使用 REST API 将数据存储在自治的 JSON 数据库(AJD)中
*   保护 REST API
*   使用 OCI 保险库存储 REST API 的秘密

这里，AJD(自治 JSON 数据库)被用作数据存储。它有许多优点，例如:

*   它为开发人员提供了模式灵活性
*   它赋予开发人员关系数据库和文档数据库的优势

**架构:**

![](img/7be8178a2973a90f0b3efeb304cee199.png)

**组件:**

为了实现这个用例，将在 OCI 租赁中部署以下 OCI 服务:

*   OCI 车厢
*   网络资源(VCN、子网、安全列表、路由表)
*   IAM 策略，动态组
*   用于调用 API、OCI 函数设置和 python SDK 的 OCI Linux VM
*   带私人通道的流池
*   服务连接器
*   OCI 函数
*   OCI 金库
*   自治 JSON 数据库

**实施步骤:**

1.  使用控制台/OCICLI/Python SDK 创建流池

![](img/ac18537c082078691a99904a83933982.png)

2.创建一个名为 produce_weather_data.py 的 python 脚本来调用天气 API

3.创建一个名为 weatherstrm 的新流，或者使用流池 OCID 获取现有流的详细信息。OCI python SDK 可用于此目的(publish_weather_data_strm.py)。

4.创建一个名为 weatherstrmfunc 的演示函数。

注意:根据以下快速入门指南，需要事先完成必要的策略和设置。

[https://docs . Oracle . com/en-us/iaas/Content/Functions/Tasks/Functions quickstartocicouteininstance . htm](https://docs.oracle.com/en-us/iaas/Content/Functions/Tasks/functionsquickstartocicomputeinstance.htm)

![](img/4536d65ab690c842016d953636892963.png)![](img/151842bd1abe3a87024422fa1a925d45.png)

5.创建服务连接器

![](img/b1fe424585df2f7198ceac392fca83e1.png)![](img/aeca5b96d183400ae3cfbf08e51c060e.png)

6.提供自治的 JSON 数据库

![](img/80a367e6c2969004e262a33d662482c6.png)

7.创建一个演示用户，并启用 rest 服务

![](img/fc9c5f1ae4a771d48c1db7bb83b8cb8f.png)

8.创建一个名为 weather_data 的表来存储天气数据

```
CREATE TABLE "DEMO"."WEATHER_DATA" 
   ( "location" CLOB constraint VALID_JSON_1 CHECK ("location" IS JSON ) , 
 "current" CLOB constraint VALID_JSON_2 CHECK ("current" IS JSON )
   ) ;
```

9.为 weather_data 表启用 rest 服务

![](img/8599e8e4e14d85f08734f73662f33940.png)

10.通过启用客户端身份验证令牌来保护 rest API

![](img/585642e4278df86f7ad43f40d3195fa9.png)

11.创建一个存储库来存储客户端身份验证令牌机密

![](img/b642d2cabc3194465bd19396d388f27a.png)![](img/69f66884712889efbf7d2c32dbb43785.png)

12.修改函数以执行以下任务:

*   以适当的 JSON 格式处理消息
*   破译信息
*   从 OCI 金库获取誓言客户端令牌
*   通过调用带有承载令牌的 REST API 将数据插入自治 json 数据库

功能详情:

a.函数. py

b.func.yaml

c.requirements.txt

13.在函数配置中添加 client_id 和 client_secret ocid，以从 OCI 保险库中获取详细信息

![](img/0d513283077c7bff7a0bca26b721a5e3.png)

是时候观察它的工作了！

*   以流的形式发布天气数据

![](img/7f2ce2556b6110cc439646161e550d57.png)

*   检查溪流

![](img/baa671dd7f29a1a64b17e7581cbe3087.png)

*   服务连接器将自动调用该功能。
*   检查功能日志

![](img/daad8569d0b55f7fc395ac7747534ab5.png)

*   检查 AJD 表中的数据。它应该以 JSON 格式插入

![](img/21a1723487e60aefd402011ded8c8f6a.png)

*   以用户友好的表格格式访问数据

```
select w."location".region,w."location".name,w."location".country, w."current".last_updated,w."current".temp_c,w."current".condition.text  FROM WEATHER_DATA w;
```

![](img/c976f4a5362c9b8ce95eebe7fc4211cd.png)

**结论:**

这只是 OCI 流和云原生服务如何帮助实时处理数据的一个简单用例。您可以将它用于本博客开头提到的其他几个用例，或者尝试其他用例。请进入我们的[开发者空闲时间](https://bit.ly/odevrel_slack)，让我们知道你在做什么，或者提出问题。当然，你也可以[在我们的免费等级](https://signup.cloud.oracle.com/?language=en)上试试这个。

快乐阅读！