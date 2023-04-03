# 从 Java 8 迁移到 Java 11

> 原文：<https://medium.com/globant/migrating-to-java-11-from-java-8-bd6343107ea6?source=collection_archive---------0----------------------->

![](img/c6ad1320b1dddc750006d7a19a26394b.png)

**概述**

甲骨文于 2018 年 9 月发布了 Java 11，这是继 Java 8 之后的长期支持(LTS)版本，也已于 2019 年 1 月停止支持 Java 8。因此，大多数公司决定升级到 Java 11 以获得最新更新，否则他们必须付费从 Oracle 获得 Oracle JDK 8 支持，或者继续使用 Oracle JDK 8 而不接收任何更新，这是不推荐的。

在本文中，我们将尝试理解如何将基于 maven 的 spring boot 应用程序从 Java 8 迁移到 Java 11。我们将了解一些先决条件、基于新版本中不推荐使用的库所做的更改，以及将应用程序迁移到 Java 11 后可能出现的一些问题。

让我们先看看一些先决条件。

**1。Java 版本**

升级 pom.xml 中的 java.version 属性。

从:
`<java.version>1.8</java.version>`
到:
`<java.version>11</java.version>`

请确保您没有将 Java 版本从 1.8 更改为 1.11，这将使您的应用程序降级到 Java 1，您不希望这样做，对吗？😄

**2。Maven 编译器**

如果您使用的是旧版本的 maven，请将其升级到最新版本，并将源和目标更新到 11。

> `*<plugin>
> <groupId>org.apache.maven.plugins</groupId>
> <artifactId>maven-compiler-plugin</artifactId>
> <version>3.8.1</version>
> <configuration>
> <source>11</source>
> <target>11</target>
> </configuration>
> </plugin>*`

类似地，我们可以使用属性定义版本，如下所示:

> `*<properties>
> <maven.compiler.source>11</maven.compiler.source>
> <maven.compiler.target>11</maven.compiler.target>
> </properties>*`

**3。Spring Boot**

将 spring boot 版本升级到 2.1.X，因为 2.0.X 及更早版本不支持 Java 11。在我们的项目中，我们选择了 2.3.4.RELEASE 版本。

> `*<plugin>
> <groupId>org.springframework.boot</groupId>
> <artifactId>spring-boot-dependencies</artifactId>
> <version>2.3.4.RELEASE</version>
> </plugin>*`

将 spring boot 升级到 2.3.4.RELEASE 也将升级其相应的依赖项，如

> ***Spring 框架到 5.2.9.RELEASE
> Tomcat 到 9.0.38
> Hibernate 到 5.4.21.Final 等等。***

[**点击此处**](https://search.maven.org/artifact/org.springframework.boot/spring-boot-dependencies/2.3.4.RELEASE/pom) 查找依赖项及其兼容版本的完整列表。

**注意:**如果你的项目只基于 Spring，那么将 Spring 框架版本升级到 5.1 将会像 Spring 5.1 和更高版本支持 Java 11 一样工作。

**如果不使用弹簧或 Spring Boot…**

正如我们大多数人所知，从 Java 9 开始，Java 已经模块化，有多个 API 和模块不再是 JDK 的一部分，如果我们想使用它们，需要显式地添加它们。JAXB、JavaFX 和 JAX-WS 是 Java 9 中弃用并在 Java 11 中删除的几个模块。

[**点击此处**](https://docs.oracle.com/en/java/javase/11/migrate/index.html#GUID-F640FA9D-FB66-4D85-AD2B-D931174C09A3) 查看部署堆栈的移除。

如果您的项目需要 JAXB 依赖项，那么下面的依赖项应该显式添加到 pom.xml 中。

> `*<dependency>
> <groupId>javax.xml.bind</groupId>
> <artifactId>jaxb-api</artifactId>
> <version>2.3.0</version>
> </dependency>*`
> 
> `*<dependency>
> <groupId>com.sun.xml.bind</groupId>
> <artifactId>jaxb-core</artifactId>
> <version>2.3.0</version>
> </dependency>*`
> 
> `*<dependency>
> <groupId>com.sun.xml.bind</groupId>
> <artifactId>jaxb-impl</artifactId>
> <version>2.3.0</version>
> </dependency>*`

**4。码头工人**

如果您的应用程序是容器化的应用程序，那么您还需要更新 Dockerfile 中的 docker 映像。我们选择了 Azul OpenJDK 图像。

`**FROM azul/zulu-openjdk-alpine:11**`

Zulu 是来自 Azul 的 OpenJDK 发行版。你可以使用任何其他的 Linux 发行版，比如 Debian，centos 等等。

[**点击此处**](https://docs.azul.com/core/zulu-openjdk/install/docker) 了解更多关于 Azul 祖鲁族分布的详情。

现在，让我们试着理解一些依赖关系的变化和相应的代码级别的变化，因为在新的 spring boot 版本和 java 11 中删除或弃用了一些库。

**1。瓜瓦卡化学管理器到咖啡因化学管理器**

在 Spring Framework 5 中，对番石榴的支持已被删除。如果您正在使用 GuavaCacheManager，那么您需要转移到 CaffeineCacheManager，它也需要一个额外的咖啡因库来添加到 pom.xml。

> `*<dependency>
> <groupId>com.github.ben-manes.caffeine</groupId>
> <artifactId>caffeine</artifactId>
> </dependency>*`

相应的 java 代码变化如下:

> *从:*`*GuavaCacheManager guavaCacheManager = new GuavaCacheManager();
> guavaCacheManager.setCacheBuilder(CacheBuilder.newBuilder()
> .expireAfterWrite(1, CACHE_TIME_UNIT));*`
> 
> *到:*`*CaffeineCacheManager cacheManager = new CaffeineCacheManager();
> cacheManager.setCaffeine(Caffeine.newBuilder()
> .expireAfterWrite(1, CACHE_TIME_UNIT));*`

[**点击此处**](/outbrain-engineering/oh-my-guava-we-are-moving-to-caffeine-99387819fdbb) 获取更多关于缓存管理器库变更的详细信息。

**2。更改 StringUtils 包基础包**

作为 spring boot 版本升级的一部分，commons-lang 库版本也有变化。它将升级到

> `*<dependency>
> <groupId>org.apache.commons</groupId>
> <artifactId>commons-lang3</artifactId>
> <version>3.10</version>
> </dependency>*`

而且 Apache Commons Lang 3 的基础包已经不是 org.apache.commons.lang 了。如果您使用的是 commons-lang 库，那么您需要将其包更改为 org.apache.commons.lang3。

从:`org.apache.commons.lang.StringUtils in Lang 2.6`到:`org.apache.commons.lang3.StringUtils in Lang 3`

**3。切换到 BouncyCastle API**

如果你在代码中使用了 **sun.security.*** 包，那么你需要从 java 9 **com.sun.*** 和 **sun 转移到**bouncy castle**API([**http://www.bouncycastle.org/**](http://www.bouncycastle.org/))。*** 包在代码中是不可直接访问的，Oracle**甚至在编译时也“保护”这些包。原因是 **com.sun.*** 和**孙。*** 包里装着内部的东西，一般情况下不应该被第三方应用使用。**

**确保将以下依赖项添加到 pom.xml 中，以便使用 Bouncycastle API。**

> **`*<dependency>
> <groupId>org.bouncycastle</groupId>
> <artifactId>bcprov-jdk15on</artifactId>
> <version>${bouncycastle.version}</version>
> </dependency>*`**

****4。莫奇托的变化****

**新版本的 Mockito 来自升级的 spring boot 版本，在此基础上做了一些更改，以使您的旧代码工作。**

> **`*a. MockitoAnnotations.initMocks(this) to MockitoAnnotations.openMocks(this)
> b. org.mockito.runners.MockitoJUnitRunner to org.mockito.junit.MockitoJUnitRunner
> c. import static org.mockito.Matchers to import static org.mockito.ArgumentMatchers*`**

****5。GC 记录变化****

**垃圾收集(GC)日志记录使用 JVM 统一日志记录框架，新旧日志记录之间存在一些差异。所以像 **PrintGCDetails** 、 **PrintGCDateStamps** 这样的标志不再起作用了。另外，**垃圾优先的垃圾收集器** (G1 GC)是 JDK 9 及更高版本中默认的垃圾收集器。考虑到这一点，您可能需要更新 JVM 日志选项。我们对 JVM 选项进行了如下所示的更改。**

> ***从:*
> `*-server -Xms2G -Xmx2G -XX:PermSize=512m -XX:+UseG1GC -XX:MaxGCPauseMillis=200 -XX:ParallelGCThreads=20 -XX:ConcGCThreads=5 -XX:InitiatingHeapOccupancyPercent=70 -XX:SurvivorRatio=8 -XX:+PrintGCDateStamps -verbose:gc -XX:+PrintGCDetails -XX:+HeapDumpOnOutOfMemoryError*`
> 
> *到:*
> `*-server -Xms2G -Xmx2G -XX:PermSize=512m -XX:+UseG1GC -XX:MaxGCPauseMillis=200 -XX:ParallelGCThreads=20 -XX:ConcGCThreads=5 -XX:InitiatingHeapOccupancyPercent=70 -XX:SurvivorRatio=8 -Xlog:gc* -XX:+HeapDumpOnOutOfMemoryError*`**

**[**点击此处**](https://docs.oracle.com/en/java/javase/11/jrockit-hotspot/logging.html#GUID-33074D03-B4F3-4D16-B9B6-8B0076661AAF) 获取更多关于 GC 相关变更的见解。**

**最后，让我们看看在升级 java 和 spring boot 版本时可能会遇到的一些问题。**

****1。Spring Boot—bean definitionoverrideexception:无效的 bean 定义****

**从 spring boot 2.1 开始，Bean 覆盖被默认禁用，必须使用 below 属性显式启用。**

**`spring.main.allow-bean-definition-overriding=true`**

****2。起因:Java . lang . classnotfoundexception:org . joda . time . year month****

**如果您使用旧版本的 joda-time 依赖项，可能会出现此问题。您需要将版本更新到 2.x 版本。**

> **`*<dependency>
> <groupId>joda-time</groupId>
> <artifactId>joda-time</artifactId>
> <version>2.10.10</version>
> </dependency>*`**

****3。用任何(T)方法测试用例失败。****

**在升级 Mockito 版本后，您可能会看到少数使用 any(T.class)方法的测试用例失败。在旧版本的 Mockito 中，any(T.class)过去表示**“任何包含 null 的引用，转换为 T 类型以避免显式转换”**，但在 2.x 版本中，它已更改为**“类 T 的任何实例”**，因此 any(T.class)将在从 1.x 升级到 2.x 期间停止匹配 null。**

**从:`any(T.class)`到:`ArgumentMatchers.nullable(T.class)`**

**[**点击此处**](https://github.com/mockito/mockito/wiki/What%27s-new-in-Mockito-2) 了解《摩奇托 2》的新功能。**

****结论****

**这是我在将我们的应用程序迁移到 java 11 时遵循的方法。我希望以上步骤能够帮助您进行项目迁移。根据项目中的依赖关系，您还会发现一些其他问题；欢迎在评论中分享你的观察，让我们一起尽可能简化 java 11 迁移过程。**

**感谢您阅读这篇文章。**

****快乐学习！****