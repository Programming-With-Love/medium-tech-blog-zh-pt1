# 使用 KPL 在现场应用中摄取 Kinesis 数据流中的数据

> 原文：<https://medium.com/globant/ingesting-data-in-kinesis-data-streams-using-kpl-for-on-premise-application-e1957a3bfe59?source=collection_archive---------0----------------------->

在亚马逊 Kinesis 中，我们可以找到不同版本的服务(Kinesis Data Firehose、Kinesis Data Analytics、Kinesis Data Streams)，在这种情况下，我使用 Kinesis Data Stream 帮助我们实时处理数据，并在我们从应用程序、日志、社交媒体源、物联网等获取数据时得到广泛使用。我有一个本地 JAVA 应用程序，它从用户的行为中生成了大量信息，所有这些信息都必须在 AWS 中查阅。所以信息流是: