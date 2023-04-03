# 从一个开发者的角度来看，Java 10 有什么值得期待的

> 原文：<https://medium.com/capital-one-tech/what-to-expect-from-java-10-one-developers-view-44b7168ebbcc?source=collection_archive---------0----------------------->

## 需要了解的 11 个基本特性

![](img/3a606035b6f93860076ea45b5c13a226.png)

多年来，Java 已经发展成为一个生态系统，而不仅仅是一种编程语言。Java 开发人员知道 Java 已经承诺了六个月的发布周期，Java 10 ( [JDK 10)](http://download.oracle.com/otndocs/jcp/java_se-10-pfd-spec/index.html) 即将发布，而 [JDK 11](http://openjdk.java.net/projects/jdk/11/) 将于秋季发布。

作为 [Java 社区进程(SM)项目](https://www.jcp.org/en/jsr/overview)的一员，我能够在 JDK 10 的早期版本上玩一玩并亲自动手。虽然有超过 100 个 JEPs 将成为 JDK 10 的一部分，但在我看来，这 11 个特性将成为游戏规则的改变者* *:*

* *请注意，对于每个版本，这些功能都通过 Java 增强流程(JEP)或 Java 社区流程(JCP)进行跟踪，其中一些功能在最终版本(GA)中可能会被丢弃。*

# **1。** **更多容器支持**

容器是软件领域最新的革命之一，它改变了软件开发和部署的模式。市场上有各种各样的集装箱解决方案，如[码头工人](https://github.com/docker/docker.github.io)、[摇杆](https://github.com/grammarly/rocker)、 [DCOS](https://dcos.io/) 等等，适用于各种用途。以下是 JDK 10 之后容器在 Java 中的工作方式的变化。

*   有了 JDK 10，Java 将会自我意识到它是运行在容器内部还是主机上。
*   JDK 10 将为容器如何控制内存和 CPU 设置引入新的 JVM 选项。这两个选项都将用于配置容器内的 JVM，以便它从 Linux CGroup 获取最大堆大小:
*   **XX:-使用容器支持**-允许禁用容器支持。默认:真，即:启用。

**-XX:+UseCGroupMemoryLimitForHeap**—选择使用/sys/fs/cgroup/memory/memory . limit _ in _ bytes 中的值作为 phys_mem 的值。

**-XX:ActiveProcessorCount = XX**—这允许覆盖 CPU 的数量。即使未启用 UseContainerSupport，也将使用此标志。

**-XX:+unlockeexperimentalvmoptions**—启用容器支持的实验性 VM 选项。

```
docker run -m 2.5G -ti — rm openjdk:10-jdk java -XshowSettings:vm -XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap -XX:MaxRAMFraction=1 -version
 Max. Heap Size (Estimated): 1.84G
```

# **2。局部变量类型推理**

允许开发人员通过增强类型推断来声明变量，换句话说，允许您在不定义关联类型的情况下声明变量。

这意味着这在 JDK 10:

*   var my list = new ArrayList<string>()；</string>
*   var my stream = list . stream()；

这将仅限于循环中的局部变量和索引。这将*不会*在 JDK 10 中对方法形式、构造器形式、方法返回类型、字段、catch 形式或任何其他类型的变量声明可用。

```
jshell> var myList=new ArrayList<>();myList ==> []jshell> myList.add(“Muktesh”);$5 ==> truejshell> myList.add(“Java10”);$6 ==> truejshell> System.out.println(myList);[Muktesh, Java10]jshell> System.out.println(myList.getClass().getName());java.util.ArrayListjshell> var x;| Error:| cannot infer type for local variable x| (cannot use ‘var’ on variable without initializer)| var x;| ^ — — ^
```

# **3。** **收藏改进**

像每个 Java 版本一样，JDK 10 对集合进行了升级。新方法 **toUnmodifiableList** 、 **toUnmodifiableSet** 和**tounmodifiablemapwhere**已经添加到流包的 Collectors 类中，允许将流的元素收集到不可修改的集合中。

# 4.**通过 Java 访问非 Java API**

也被称为[巴拿马项目](http://openjdk.java.net/projects/panama/)。这个 Java 社区正在努力为非 Java APIs 敞开大门(其中很多都是原生 C/C++接口)。这将实现:

*   在堆中创建新的数据布局。
*   JVM 的本机元数据定义。
*   面向本机的 JIT 优化。
*   从 JVM 或 JVM 内部访问本机数据
*   此外，JVM 正试图引入专门为本地语言开发的面向本地的解释器

# 5.**多语言 VM**T21【支持

也被称为[项目 Graal](https://github.com/oracle/graal) ，这允许通过 API 公开 VM 功能。这是一个为 JVM 展示新的多语言运行时的努力。这可以通过以下方式实现:

*   Graal —动态编译器。
*   用于实现使用 Graal 作为编译器的语言。
*   利用 Graal 实现 Java 应用程序的提前编译。
*   Graal SDK—Graal VM 的 API 集合。

# **6。** **应用类-数据共享**

类数据共享在 Java 中已经存在很长时间了。它有很多好处，比如通过在不同的 Java 进程之间共享相同的数据来提高 JVM 的启动性能和减少内存。

*   在 JDK 10 中，内置系统类装入器、内置平台类装入器和自定义类装入器可以装入存档的类。以前，它仅限于引导类装入器。

-XX:+UseAppCDS 将允许所有的类加载器共享数据。

*   此外，还可以通过只允许存档指定类别集来关闭它。这可以通过关闭整个共享并仅启用选定的共享来实现。

```
Jshell> java -Xshare:off -XX:+UseAppCDS -XX:DumpLoadedClassList=com.test -cp Test.jar EntryClass
```

# **7。** **改进根证书配置 aka Cacerts**

cacerts 密钥库(JDK 的一部分)包含一组根证书。目前，它是空的，这就是为什么配置安全协议(如 TLS)会变得如此困难。在 JDK 10 中，它将在 JDK 提供一组默认的根认证证书。

# **8。垃圾收集的改进**

与其他版本一样，[垃圾](http://openjdk.java.net/jeps/304) [收集](http://openjdk.java.net/jeps/307)将在 JDK 10 中得到改进。这包括:

*   G1 垃圾收集器的并行完全垃圾收集。G1 在 JDK 9 中成为默认 GC，现在通过允许完全 GC 并行变得更加强大。
*   添加了垃圾收集器接口，这将使用户能够配置替代的垃圾收集器。

# **9。** **在多个设备上分配堆**

这一改变将使用户能够将 JVM 堆分配到多个设备上。这是一个 WIP，但将支持大型应用程序(如大数据应用程序)在多个堆中运行。[到目前为止，还没有完全决定哪些内容将留在 DRAM 中，哪些内容将放在备用内存位置中。](http://openjdk.java.net/jeps/316)

*   JVM 中将引入一个新的标志 XX:AllocateHeapAt= <path>,这将允许在备用路径上分配堆。</path>

# **10。线程局部握手**

JDK 10 将通过允许在线程上执行回调而无需执行全局虚拟机安全点来提高并发代码的效率。这将使停止单个线程变得更加容易和便宜。它还将改善虚拟机延迟，因为它将花费更少的精力来查询全局虚拟机中的线程状态。

# 11.**通用数据类型创建或通用运行时类型**

也被称为[项目 Valhalla](http://openjdk.java.net/projects/valhalla/) ，这将允许开发人员在堆中定义平面数据类型——即无引用数据类型。这将减少内存使用并提高局部性。

总的来说，Java 似乎正在为新的改进计划一个频繁的发布周期，路线图的核心领域集中并致力于容器化和优化。随着每六个月推出一个新版本，随着时间的推移，我们将继续看到一系列令人兴奋的改进。

我鼓励大家亲自试用 JDK 10，看看这些新功能，包括我没有在这里列出的功能。

*披露声明:这些观点是作者的观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权均为其各自所有者所有。本文为 2018 首都一。*