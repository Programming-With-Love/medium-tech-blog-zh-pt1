# 从 Java 到 JavaScript 的调用(带回调)——利用 GraalVM 的互操作性支持

> 原文：<https://medium.com/oracledevs/calling-out-from-java-to-javascript-with-call-back-leveraging-interoperability-support-of-bc6193c6177e?source=collection_archive---------0----------------------->

从 Java 到 JavaScript 的互操作性一直是 Java 社区的目标。在 Rhino 和后来的 Nashorn 中，两次勇敢的尝试将脚本交互添加到 JDK 和 JVM 中。现在，有了 GraalVM，从 Java 应用程序中运行 JavaScript 代码有了更好的选择。交互本身更快、更健壮、更“原生”(而不是附加)。对于开发人员来说，这种交互更容易实现。还有一个好处是:…