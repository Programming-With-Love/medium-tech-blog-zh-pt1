# 仅 SQL 计数间隔足够长的记录—三种解决方案

> 原文：<https://medium.com/oracledevs/sql-only-counting-records-sufficiently-spaced-apart-using-analytics-with-windowing-clause-and-anti-b6a2a09a06bd?source=collection_archive---------5----------------------->

一位同事向我提出了一个很好的 SQL 挑战。挑战基本上就是这张桌子。表包含描述登录事件的记录。每条记录都有一个登录时间戳和登录人员的标识符。挑战在于计算“唯一的”登录事件。这些时间段被定义为个人登录的唯一且不重叠的两小时时间段。例如，某个时间段以登录开始，该登录在同一个人的上一个时间段开始后至少两个小时。