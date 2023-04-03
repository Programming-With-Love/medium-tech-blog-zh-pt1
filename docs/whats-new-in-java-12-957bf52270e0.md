# Java 12 的新特性

> 原文：<https://medium.com/quick-code/whats-new-in-java-12-957bf52270e0?source=collection_archive---------4----------------------->

让我们来看看 Java 12 里面有什么。与之前的版本相比，新的 Java 版本包含的主要增强较少:Java 12 中的 [8](https://openjdk.java.net/projects/jdk/12/) JEPs 与 Java 11 中的 [17](https://openjdk.java.net/projects/jdk/11/) JEPs。正如你当然记得，JEP 主张 JDK 增强提案。Java 11 在吉拉也有更多的封闭条目:Java 11 中的~ [2700](https://bugs.openjdk.java.net/browse/JDK-8218034?jql=project%20%3D%20JDK%20AND%20resolution%20in%20(Fixed%2C%20Delivered)%20AND%20fixVersion%20%3D%20%2211%22) ，而 Java 12 中的~ [2400](https://bugs.openjdk.java.net/browse/JDK-8219012?jql=project%20%3D%20JDK%20AND%20resolution%20in%20(Fixed%2C%20Delivered)%20AND%20fixVersion%20%3D%20%2212%22) 。但现在只是 2019 年 2 月中旬，也许他们可以在 2019 年 3 月 19 日 Java 12 计划发布时交付 300 个吉拉条目。现在让我们仔细看看 Java 12 中有什么。

# JEP 189: Shenandoah:一个低暂停时间的垃圾收集器(实验)