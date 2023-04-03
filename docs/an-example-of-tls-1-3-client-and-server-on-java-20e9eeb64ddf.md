# Java 上 TLS 1.3 客户端和服务器的示例

> 原文：<https://medium.com/quick-code/an-example-of-tls-1-3-client-and-server-on-java-20e9eeb64ddf?source=collection_archive---------4----------------------->

Java 11 支持 2018 年 8 月发布的 TLS 1.3 协议。在实施新的 TLS 协议的过程中，Java security-libs 团队对 Java 安全套接字扩展(JSSE) 进行了重大改进。我曾经在 Java 的安全库上工作了 6 年，所以我可以肯定这不是一个简单的任务。尽管如此，Java security-libs 团队在 Java 11 中交付了 TLS 1.3 实现。干得好！

但是 Java 11 中的 TLS 1.3 实现并不支持新 TLS 协议的所有特性。以下是 JSSE 支持的(详见 [JEP](http://openjdk.java.net/jeps/332) …