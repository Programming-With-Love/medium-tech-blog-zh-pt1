# 测试通用模块

> 原文：<https://blog.kotlin-academy.com/testing-common-modules-66b39d641617?source=collection_archive---------5----------------------->

在具有[良好架构](/architecture-for-multiplatform-development-in-kotlin-cc770f4abdfd)的 Kotlin 多平台项目中，我们在`common-client`公共模块中拥有完整的业务逻辑。这样，它可以在客户端之间共享。

事情是**业务逻辑需要被单元测试**并且公共模块测试不像常规模块测试。在本文中，我将展示这不仅是可能的，而且非常方便，这要感谢 Kotlin 团队给我们的工具。

我们将学习来自 Kt 的例子。学院申请。

![](img/1fe5615bb242294907539b9aebde1211.png)

# 工具

为了支持通用模块单元测试，Kotlin 团队制作了`kotlin.test`库。要使用它，我们需要将以下依赖项添加到您的公共模块`build.gradle`:(添加到`common-client`)

```
**testCompile** "org.jetbrains.kotlin:kotlin-test-common:$kotlin_version" 
**testCompile** "org.jetbrains.kotlin:kotlin-test-annotations-common:$kotlin_version"
```

一旦你有了它，你就可以在这个模块中编写测试了。这里有一个例子:

```
@Test
**fun** twoSideConversionTest() {
    **val** dateFormatted = **"2018-10-12T12:00:01"** *assertEquals*(
        dateFormatted, 
        dateFormatted.*parseDate*().toDateFormatString()
    )
}
```

正如你所看到的，我们使用`@Test`注释来标记测试用例。类似于 JUnit 中的。您还可以使用其他注释:

*   `@Test` —用于测试标记功能。
*   `@BeforeTest` —标记函数，以便在类中每次测试之前调用它。
*   `@AfterTest` —标记函数，以便在类中的每个测试之后调用它。
*   `@Ignore` —标记忽略已定义的测试。

您还可以使用一些预定义的函数来生成断言:

*   `assertTrue`/`assertFalse`—断言谓词为真。
*   `assertEquals` / `assertNotEquals` —断言两个值相等(使用相等运算符`==`检查)
*   `assertSame`/`assertNotSame`—断言两个值在引用上相等(使用引用相等运算符`===`进行检查)
*   `assertNull` / `assertNotNull` —断言值等于`null`。
*   `assertFails` / `assertFailsWith` —断言代码块返回它应该返回的异常。
*   `expect` —断言功能块返回预期值。
*   `fail` —用于标记在测试执行期间不应该运行的代码部分。

几乎所有上述函数都接受值(`assertTrue(users.number == 1)`)或函数(`assertTrue { users.number == 1 }`)。

使用上述函数，我们可以很容易地为我们的业务逻辑编写单元测试。但是，如果公共模块本身并不例外，并且需要被编译到某个平台(JVM、JS 或 Native)上，这是如何运行的呢？答案是我们实际上需要在平台模块上运行测试(`common-client-js`和`common-client-jvm`)。这就是为什么我们还需要向平台模块添加额外的依赖项。在 JVM 平台中添加以下依赖项:

```
**testCompile** "org.jetbrains.kotlin:kotlin-test-junit:$kotlin_version"    **testCompile** "org.jetbrains.kotlin:kotlin-test:$kotlin_version"
```

现在您可以使用`common-client-jvm`上的 test 命令在 JVM 上运行测试:

```
./gradlew :common-client-jvm:test
```

JavaScript 测试目前还不是现成的，它需要更多的配置。检查[该配置](https://github.com/MarcinMoskala/KotlinAcademyApp/blob/cc090bb5acaa7e26fc26952a4c6b6290e5612c5c/common-client-js/build.gradle)。这接近于使用 [Mocha 框架](https://mochajs.org/)进行单元测试的最低配置。设置完成后，您可以使用 Gradle 运行测试:

```
./gradlew :common-client-js:test
```

请记住，在所有平台上运行测试非常重要。这是因为它们有不同的实现，它们可以有不同的实际声明，所以测试在单个平台上通过并不意味着它们也可以在其他平台上运行。

![](img/3947aac7408c1b3caf1e021b1eb2da2d.png)

# 命名测试

用完全描述性的名称来命名测试真的很好。比如这个例子:

```
@Test
**fun** `When onCreate, loads and displays list of news`() {
    **val** view = NewsView()
    overrideNewsRepository **{** NewsData(**FAKE_NEWS_LIST_1**) **}
    val** presenter = NewsPresenter(view)
    *// When* presenter.onCreate()
    *// Then
    assertEquals*(**FAKE_NEWS_LIST_1**, view.**newsList**)
    view.assertNoErrors()
}@Test
**fun** `BasePresenter is cancelling all jobs during onDestroy`() {
    **val** jobs = (1..10).*map* **{** makeJob() **}
    val** presenter = **object** : BasePresenter() {
        **fun** addJobs(jobs: List<Job>) {
            **this**.**jobs** += jobs
        }
    }
    presenter.addJobs(jobs)
    *// When* presenter.onDestroy()
    *// Then
    assertTrue*(jobs.*all* **{ it**.**cancelled }**)
}
```

唯一的问题是 JavaScript 不允许这样的名字，一旦我们试图在 JS 模块中运行它，就会得到一个错误。有一个简单的解决方案:Kotlin/JS 提供了注释`JsName`，可以用来在编译后的代码中指定函数的实际名称。它是 Kotlin/JS 注释，所以不能在公共模块中使用。虽然，我们可以在公共模块`common-client`的测试源中定义预期的声明:

```
**expect annotation class** JsName **constructor**(**val name**: String)
```

在`common-client-js`模块上做`kotlin.js.JsName`注释:

```
**actual typealias** JsName = kotlin.js.JsName
```

定义一些将在`common-client-js`上被忽略的注释:

```
**actual annotation class** JsName **actual constructor**(
    **actual val name**: String
)
```

现在，我们需要注释我们的测试:

```
@JsName(**"gettingAndDisplayingTest"**)
@Test
**fun** `When onCreate, loads and displays list of news`() {
    // ...
}@JsName(**"cancellingJobTest"**)
@Test
**fun** `BasePresenter is cancelling all jobs during onDestroy`() {
    // ...
}
```

有了这样的声明，我们可以自由地在两个平台上运行我们的测试。由于这一点，我们的测试报告将显示全名。JVM 和 JS 都适用！这是我从摩卡发来的报道的标题:

```
BasePresenterUnitTest
  ✓ BasePresenter is canceling all jobs during onDestroy
DateTimeUnitTest
  ✓ Two-way conversion should give the same result
  ✓ Ordering is correct after parse
FeedbackPresenterUnitTest
  ✓ Sends all data provided in form
  ✓ When sending feedback, loader is displayed
  ✓ When repository returns error, it is shown on view
  ✓ After data are sent, view is switching back to news list
```

![](img/7fa02f190f5212e33748dc81a36a38f9.png)

# 异步单元测试

有时我们需要阻塞当前线程一段时间，以检查是否有其他进程发生。我们在`PeriodicCaller`测试中就有这样的情况。这个类假定每隔给定的毫秒数调用一次提供的函数。下面是它的实际实现:

```
**class** PeriodicCallerImpl : PeriodicCaller {
  **override fun** start(timeMillis: Long, callback: () -> Unit): Job { 
    **return** *launchUI* **{
      while** (**true**) {
        *delay*(timeMillis)
        callback()
      }
    **}
  }
}**
```

但是我们怎么知道它是如何工作的呢？一个简单的解决方案是设置它每隔 50 毫秒调用一个函数，等待 1 秒，检查函数是否被调用了大约 20 次。下面是以下测试的实现:

```
@Test
**fun** `Periodic caller for 50ms is called around 20 times during 1 second`() = *runBlocking* **{
  val** caller = PeriodicCaller.PeriodicCallerImpl()
  **var** count = 0
  **val** job = caller.start(50) **{** count++ **}** *delay*(1000)
  job.cancel()
  *assertTrue*(count **in** 18..22)
**}**
```

它是如何工作的？首先，我们在协程中运行我们的测试，这意味着`delay`不是`Thread.delay`，它不会停止线程。相反，它挂起了协程(你可以在这里阅读[关于差异的](https://kotlinlang.org/docs/reference/coroutines.html#blocking-vs-suspending))。虽然，如果我们使用`launch`或者`runBlocking`，那么测试执行将在第一次断言之前完成。`runBlocking`是一种非常特殊的协程生成器，一旦协程被挂起，它就会阻塞线程。(我想这听起来真的很复杂，但是当你理解协程是如何工作的时候就不复杂了。很快我将为 Kt 写更多关于协程的文章。学院，所以[订阅](/new-series-and-channels-346f1a4a4420)如果你想得到通知。)

上面的测试非常适合 Kotlin/JVM。更大的问题是 Kotlin/JS。原因是 JavaScript 是单线程的，这一个线程不能被阻塞。如果没有阻塞，我们无法在延迟时间结束之前阻止测试结束。这看起来像帕特，但科特林团队提供了解决方案。

正如你在本期中看到的，Kotlin 团队正计划支持暂停测试。他们还提供了一个变通方法:我们可以为 Kotlin/JVM 创建一个使用`runBlocking`的函数，并为 Kotlin/JS 返回 JavaScript `Promise`。有些框架对待`Promise`就像挂起测试一样，比如[摩卡](https://mochajs.org/)，`Promise`里面的代码可以自由挂起，测试会一直等到结束。要实现它，我们需要下面的预期声明:(你可以在`common-client`的`test`源集中定义它)

```
**expect fun** <T> runTest(block: **suspend** () -> T)
```

这是它对 JVM 的实际声明:

```
**actual fun** <T> runTest(block: **suspend** () -> T) {
    *runBlocking* **{** block() **}** }
```

这是它对 JS 的实际声明:

```
**actual fun** <T> runTest(block: **suspend** () -> T): **dynamic** = *promise* **{** block() **}**
```

注意，我们使用了`dynamic`来欺骗预期的声明。它期望得到`Unit`，但是在 Kotlin/JS 中可以返回`dynamic`，尽管它实际上是`Promise`。

现在，我们需要使用这个函数来包围整个测试，它将在 JVM 和 JS 测试中正确工作:

```
@Test
@JsName(**"numberOfCallsInTimeTest"**)
**fun** `Periodic caller for 50ms is called around 20 times during 1 second`() = *runTest* **{
    val** caller = PeriodicCaller.PeriodicCallerImpl()
    **var** count = 0
    **val** job = caller.start(50) **{** count++ **}** *delay*(1000)
    job.cancel()
    *assertTrue*(count **in** 18..22)
**}**
```

这样我们就可以轻松地编写多平台并发测试。

# 摘要

如你所见，通用模块单元测试已经非常成熟了。很快我们将向您展示如何在公共模块中使用全功能的模仿库[mock](http://mockk.io/)。现在，我们可以自信地说，你可以单元测试一切。在本文中，我向您展示了如何做到:

*   使用`kotlin.test`注释和函数来改进测试。
*   使用 Kotlin 描述性函数名，使测试更容易理解。
*   实现并发测试。

检查一下 [Kt。学院应用项目及其测试](https://github.com/MarcinMoskala/KotlinAcademyApp)。请随意试验它们，并添加一些新的测试。我们很乐意接受您的贡献或给您反馈:)

## 学到了什么？单击👏说“谢谢！”并帮助他人找到这篇文章。

如果你认为这很重要，与他人分享。

你需要 Kotlin 工作室吗？访问我们的网站,看看我们能为您做些什么。

我要感谢 Oleksiy Pylypenko 对这篇文章的校对。

关于这个主题的下一篇文章将在 Kt 上发表。学院，所以记得订阅。也可以关注[我的推特](https://twitter.com/marcinmoskala) ( [@marcinmoskala](https://twitter.com/marcinmoskala) )或者 Kt。学院推特([@ ktdotsacademy](https://twitter.com/ktdotacademy))。

[![](img/5ce68714efe3efc036e06786166954ff.png)](http://eepurl.com/diMmGv)![](img/77f73b2843c7321d074810231e681255.png)