# 查询狙击手

> 原文：<https://medium.com/square-corner-blog/query-sniper-b4be1912c1f6?source=collection_archive---------3----------------------->

## 控制失控查询。

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

想象一下:您的服务接收到一个请求并启动一个数据库查询；请求超时，查询仍在运行，导致不必要的数据库负载，并可能导致数据库资源不足。当服务自动重试请求时，问题只会加剧，并会使最强大的数据库服务器屈服。

那么，如何防范这种情况呢？

虽然这是许多平台的一个相当普遍的问题，但我们只涉及运行在 [MySQL](http://www.mysql.com/) 后端的 [Java](http://java.sun.com/) 和 [JDBC](http://www.oracle.com/technetwork/java/javase/jdbc/index.html) 。然而，通篇讨论的思想应该适用于其他平台和数据库。

# 数据库超时

MySQL 的最新版本允许设置全局超时，这样任何查询的运行时间都不会超过预定义的时间。这是一种相当粗粒度的方法，因为必须为可能运行时间最长的查询设置限制。所以，这种方法被排除了。(另外，我们没有在生产中运行 MySQL 的最新版本。)

# 应用程序级超时

您可以在 MySQL 的 JDBC 驱动程序上设置超时。我们使用 [HikariCP](https://github.com/brettwooldridge/HikariCP) 进行连接池；它还允许您相应地配置池，委托驱动程序设置超时。这听起来很有希望。

# MySQL 的 JDBC 驱动程序

对于任何读过 MySQL 的 JDBC 驱动程序 [Connector/J](https://dev.mysql.com/downloads/connector/j/) 的源代码的人来说，在这一点上你可能会感到害怕。这个驱动是在 Java 1.3 风靡一时的时候写的，只收到了一些小的更新。代码是意大利面条；充斥着粗糙的同步，通常不够灵活，不太具有可扩展性。

![](img/469cb0c7878a6d971fa996e6e345e926.png)

哦好吧。

深入到驱动程序中，我看到为每个连接实例启动了一个 JDK [计时器](https://docs.oracle.com/javase/8/docs/api/java/util/Timer.html),并计划在预定义的时间过后运行一个清理任务。在*com . MySQL . JDBC . statement impl*中我们看到:

```
**public** **class** **StatementImpl** **implements** Statement **{**  
  **...**
  **class** **CancelTask** **extends** TimerTask **{**    
    **...**    
    @Override
    **public** **void** **run()** **{**
        Thread cancelThread **=** **new** **Thread()** **{**
            @Override
            **public** **void** **run()** **{**
  **...**
**}**
```

# 清理任务

驱动程序安排的清理任务很简单(也在*com . MySQL . JDBC . statement impl*中):

```
**..**
cancelConn **=** StatementImpl**.**this**.**connection**.**duplicate**();**
cancelStmt **=** cancelConn**.**createStatement**();**
cancelStmt**.**execute**(**"KILL QUERY " **+** CancelTask**.**this**.**connectionId**);**
**...**
```

除了清除语句实例上的状态(在一些非常糟糕的同步块中)，该任务还执行两个关键操作:

1.  克隆 JDBC 连接以建立到数据库的新的*连接。*
2.  对新连接发出*终止查询< connection_id >* 查询，传入原始连接的 id。

# 缺点

除了两个问题之外，这样做很好:

## 定时器

JDK 的计时器又旧又笨重，而且很贵。每个线程创建一个新线程。(即使是 Javadocs on *Timer* s 也推荐使用[*ScheduledExecutorService*s](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ScheduledExecutorService.html)代替。)

## 克隆连接

每次克隆 JDBC 连接都会导致数据库服务器可用的连接数量出现问题——尤其是在处理占用资源的失控查询时。这样的超时任务可能无法连接到数据库来终止失控的查询。

# 质疑狙击手

由于这些缺点，我们决定编写自己的查询狙击程序，而不是简单地设置 JDBC 超时。query sniper 实际上做的事情与在 JDBC 连接上设置超时完全相同，只是它被实现为 JOOQ[*execute listener*](http://www.jooq.org/doc/3.7/manual/sql-execution/execute-listeners/)并向单个*scheduled executorservice*提交任务。每次执行查询时，整个系统只配置一个维护线程。这是这个想法的一个简化版本:

```
package querysniper**;**import java.util.concurrent.ScheduledExecutorService**;**
import org.jooq.ExecuteContext**;**
import org.jooq.ExecuteListenerProvider**;**
import org.jooq.Query**;**
import org.jooq.impl.DefaultExecuteListener**;**import static java**.**util**.**concurrent**.**TimeUnit**.**MILLISECONDS**;****public** **class** **QuerySniper** **extends** DefaultExecuteListener **{**
  **private** **static** **final** **int** MAX_QUERY_TIME **=** 10_000L**;** *// 10 seconds***private** **final** ScheduledExecutorService scheduledExecutorService**;**
  **private** **volatile** **boolean** queryRunning **=** **false;****public** **QuerySniper(**ScheduledExecutorService scheduledExecutorService**)** **{**
    **this.**scheduledExecutorService **=** scheduledExecutorService**;**
  **}**@Override
  **public** **void** **executeStart(**ExecuteContext ctx**)** **{**
    *// Start a timer*
    queryRunning **=** **true;***// You'd typically have this hook into your system to understand how much*
    *// time to let your query run for*
    **long** timeBudget **=** MAX_QUERY_TIME**;**scheduledExecutorService**.**schedule**(()** **->** **{**
      **if** **(**queryRunning**)** **{**
        log**(**ctx**,** timeBudget**);**
        **if** **(**ctx**.**query**()** **!=** **null)** **{**
          ctx**.**query**().**cancel**();**
        **}**
        queryRunning **=** **false;**
      **}**
    **},** timeBudget**,** MILLISECONDS**);**
  **}**@Override
  **public** **void** **executeEnd(**ExecuteContext ctx**)** **{**
    *// Stop timer*
    queryRunning **=** **false;**
  **}**@Override
  **public** **void** **exception(**ExecuteContext ctx**)** **{**
    *// Stop timer on exception as well*
    queryRunning **=** **false;**
  **}****private** **void** **log(**ExecuteContext ctx**,** **long** timeBudget**)** **{**
    System**.**out**.**println**(**"Query exceeded %s ms. Killing it!"**,** timeBudget**);**
    System**.**out**.**println**(**"Query: %s"**,** ctx**.**sql**());**
  **}**
**}**
```

使用 JOOQ 的[*configuration . set()*](http://www.jooq.org/javadoc/3.7.0/org/jooq/Configuration.html#set-org.jooq.ExecuteListenerProvider...-)API 将查询狙击手添加到所有 JOOQ DSL 会话中:

```
DefaultConfiguration cfg **=** getDefaultConfiguration**();**
ScheduledExecutorService executor **=** getScheduledExecutorService**();**
cfg**.**set**(()** **->** **new** **QuerySniper(**executor**));**
```

# 细粒度超时

为了额外的好处，查询狙击手检查请求上下文，并确定在请求超时之前还有多少时间来运行查询；因此，在每个查询的基础上设置细粒度的超时。

# 再次克隆连接

我们希望维护一个单独的、预先建立的由单个连接组成的连接池，专门用于让查询狙击手杀死长时间运行的查询。这样，我们就克服了无法建立新的数据库连接的问题。但是，我们遇到了阻碍。为了维护我们自己的连接池，我们必须手工创建我们自己的*KILL QUERY<connection _ id>*语句。MySQL 的 JDBC 驱动程序不公开事务 ID 来允许我们这样做。

遗憾的是，查询狙击手最终只是在它想要终止的运行语句上调用了[*statement . cancel()*](https://docs.oracle.com/javase/8/docs/api/java/sql/Statement.html#cancel--)。这仍然会导致连接被克隆，等等。就像以前一样。

![](img/34a09478f5b93fb69f059869efbfbbd5.png)

# 结论

有了我们的查询狙击手，并在许多系统上运行于生产环境中，我们为自己节省了相当多的停机时间——这些停机时间我们以前在测试我们构建的有一些流氓查询的新系统时见过。如果不是 it 击落了这些被放弃的查询，这些中断还会继续发生(根据查询狙击手记录的活动来判断)。

# 下一步是什么？

尽管我们很想开源这段代码，但它与 Square 的基础设施联系太紧密，无法提取到一个单独的库中。(如果你想看更多，你可以随时[加入我们的团队](https://squareup.com/careers)。)但是，我希望上面描述的模式和包含的代码片段对其他人有用。

我真的希望能够保持与数据库的持久连接。为此，我希望修补 MySQL 的驱动程序，以暴露其事务 ID，允许手工制作的 *KILL QUERY* …语句。

或者，也许我真正需要做的，是使用并发和线程安全、资源管理、可配置性和可扩展性的现代理念重写 MySQL JDBC 驱动程序？；)