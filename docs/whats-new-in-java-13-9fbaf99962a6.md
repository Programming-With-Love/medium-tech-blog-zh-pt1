# Java 13 的新特性

> 原文：<https://medium.com/quick-code/whats-new-in-java-13-9fbaf99962a6?source=collection_archive---------0----------------------->

Java 13 将于 2019 年 9 月 17 日发布。除了约 2300 个错误修复和小的增强，新版本的 Java 包含 5 个主要增强，也称为 JEPs (Java 增强建议)。让我们仔细看看这些主要更新:文本块，开关表达式，重新实现了传统的套接字 API，更新 ZGC 和动态 CDS 档案。

# JEP 355:文本块(预览)

有时候我们需要在 Java 中定义一个很长的多行字符串。例如，它可以是 HTML 页面或 SQL 查询。它通常看起来是这样的: