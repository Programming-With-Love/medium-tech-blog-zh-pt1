# 使用 Kotlin 对 RESTful 微服务进行黑盒 API 测试

> 原文：<https://medium.com/capital-one-tech/blackbox-api-testing-with-kotlin-for-restful-microservices-183adc2b8088?source=collection_archive---------0----------------------->

![](img/ad1375565bf099daeb98963ddcd736e3.png)

80%代码行(LOC)测试覆盖率给你带来了什么？100%怎么样？它们让你*确定在同一个系统中，有一段代码运行另一段代码，并且系统的大部分被覆盖*。这是假设你没有配置你的声纳设置排除' * '。

**这有什么问题吗？**部分原因是我们衡量的东西，部分原因是我们衡量的方式。在这种情况下，您根本无法保证真正重要的东西得到了测试。*如果汽车发动不起来，螺母拧紧有什么关系？*

行为驱动开发 (BDD)和测试的“规范”概念在某种程度上减轻了这种担忧。它改变了对度量多少代码的关注，而是回答了问题*“它做了它应该做的事情吗？”*

当我们进入微服务领域时，我们不能再依赖由 BDD 测量的自洽系统作为功能完整性的唯一度量。就单元测试和 BDD 测试而言，服务 A 和服务 B 可能都是完美的，但是从客户端的角度来看，它们的互操作性可能会被彻底破坏。*我们可以确保螺母拧紧，汽车启动，但不能确保汽车餐厅正确地制作了汉堡。*

**最近，我甚至断言**如果我通过外部黑盒测试套件(实际上是功能集成测试)覆盖了整个系统，那么我已经在很大程度上忽视了 LOC 指标的价值。当然，我仍然期望开发人员编写单元测试和 BDD 测试——这些只是使服务自我一致和良好开发的工具。对我来说真正重要的是，我有用例覆盖(UCC ),通过用户旅程跟踪与我们服务的交互，就像一个真实的客户会做的那样。这些测试是在微服务之间端到端地运行产品，同时考虑正面和负面的结果。

有一些厂商尝试将它作为一个工具套件提供[——特别是 smart bear](https://smartbear.com/)——但是这涉及到许可他们的工具和在 GUI 中工作。当然，我们可以在 [Cucumber](https://cucumber.io/) 中定义这些用户旅程的使用规范，但是之后我们必须学习[小黄瓜](https://github.com/cucumber/cucumber/wiki/Gherkin)，并在它们的模板化方法中工作。

那么，我们有什么选择呢？一种是卷起袖子，将我们的验收标准转化为代码形式的用户旅程。*让我们确保我们有车钥匙，我们可以进入汽车，车轮启动，汽车启动，我们可以到达汽车餐厅，点汉堡，回家，并验证汉堡返回 OK 200。*

为了更好地工作，代码应该存在于实际服务本身之外。根据应用程序的规模和复杂程度，可能不止一个。

# 这些是碎片

*   **科特林**

我最近采用的方法是放心地使用[科特林](https://kotlinlang.org/)。如果您没有听说过 Kotlin，并且您在 JVM 上运行，那么您就错过了机会。它是由 JetBrains 的人创造的。虽然它有点模糊，但随着 [Pivotal 开始真正推动它](https://spring.io/blog/2017/01/04/introducing-kotlin-support-in-spring-framework-5-0)牵引力即将到来。不幸被命名为 SparkJava 的是一个专注于 Kotlin 的微服务框架。不管怎样，你会听到越来越多关于科特林的消息。这就像没有砂纸的 Scala。

*   **KotlinTest**

模仿奇妙的 ScalaTest 库，[kotlistest](https://github.com/kotlintest/kotlintest)闻起来几乎一模一样；而且很像一般的语言差异，甚至更容易。它是一个规范驱动的 BDD 测试工具包，有很多钩子可以根据需要管理您的测试和套件。

*   **放心**

[放心](https://github.com/rest-assured/rest-assured)是一个以最小的摩擦进行 Rest 调用并断言其结果的工具。因为它是 Java，所以你可以在 Kotlin 中使用它。

有了这三个简单的部分，我就有了开始构建一个与我的微服务交互的功能群所需的一切，以及基于这些功能创建用户旅程的测试。

# 辅导的

如果你只是想深入研究代码，从这里开始:[https://github.com/revelfire/blackboxmicroservicetesting](https://github.com/revelfire/blackboxmicroservicetesting)

*注意——运行此代码的具体说明在该报告的 readme.md 中，此处不再重复。*

# 堆

**格雷尔**

科特林

**科特林测试**

**Intellij Kotlin 插件**

**重新发布**

真的就这些了。

# 结构

我们将代码分解到与特定结构功能相匹配的文件夹中。

**/功能**

该文件夹专门用于充当 API 客户端的函数。这里一般不应该有测试，只有函数。

**/测试**

该文件夹专门用于验证特定 API 函数调用的单个测试/场景。这将包括积极和消极的测试案例。

**/旅程**

该文件夹专门用于将多种功能组合到用户旅程中的测试，这些测试应该理想地反映具有记录的验收标准的应用程序用例。

# 片

**build.gradle**

这个文件将所有的片段缝合在一起，并为我们提供了简单的文本执行。

```
buildscript {
    ext.kotlin_version = "1.1.4"
    repositories {
        mavenLocal()
        mavenCentral()
    }
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    }
}repositories {
    mavenLocal()
    mavenCentral()
}apply plugin: 'java'
apply plugin: 'idea'
apply plugin: 'maven'
apply plugin: 'kotlin'group 'com.revelfire.test.api'
version '1.0-SNAPSHOT'sourceCompatibility = 1.8
targetCompatibility = 1.8dependencies {
    compile group: 'org.jetbrains.kotlin', name: 'kotlin-stdlib', version: "$kotlin_version"//Include our app lib so we have access to models (not mandatory, but helpful)
    compile group: 'com.revelfire', name: 'car-service', version: '1.0-SNAPSHOT'compile group: 'com.fasterxml.jackson.module', name: 'jackson-module-kotlin', version: '2.9.0'compile group: 'io.rest-assured', name: 'rest-assured', version: '3.0.2'
    compile group: 'org.hamcrest', name: 'hamcrest-core', version: '1.3'
    compile group: 'org.jetbrains.kotlin', name: 'kotlin-stdlib', version: "$kotlin_version"
    compile group: 'io.kotlintest', name: 'kotlintest', version: "$kotlin_version"
    compile group: 'com.github.javafaker', name: 'javafaker', version: "0.13"

    testCompile group: 'io.kotlintest', name: 'kotlintest', version: '2.0.1'
}tasks.test.doFirst {
    def includeTags = System.properties['includeTags']
    if(includeTags) {
        systemProperty 'includeTags', includeTags
    }
    testLogging.events = ['passed','failed','skipped']
}tasks.test.outputs.upToDateWhen { false }
```

**TestBase.kt**

该文件为测试执行提供了一些基本的基础，并为总体功能提供了挂钩。

```
package app//…imports omitted…object QuickRun : Tag()
object Integration : Tag()    //Represents a single unit of testing an API endpoint (various scenarios possibly)
object Journey : Tag()        //Represents a group of tests that mimic a user behavior/journey through the appobject TestBase {var props:Properties = Properties();init {
        props = readProperties()
        validateProperties()
    }fun carServiceHost():String = "${getProperty("service.car-service.url")}:${getProperty("service.car-service.port")}"
    fun foodServiceHost():String = "${getProperty("service.food-service.url")}:${getProperty("service.food-service.port")}"fun validateProperties() {}fun getPropertyAsInt(key:String):Int {
        val value = getProperty(key)
        value?.let {
            return value.toInt()
        }
        throw RuntimeException("Property ${key} not found!")
    }fun getPropertyAsBoolean(key:String):Boolean {
        val value = getProperty(key)
        value?.let {
            return value.toBoolean()
        }
        throw RuntimeException("Property ${key} not found!")
    }fun getProperty(key:String):String? {
        System.getProperty(key)?.let {
            return System.getProperty(key)
        }
        return props[key] as String
    }fun getRequiredProperty(key:String):String? {
        System.getProperty(key)?.let {
            return System.getProperty(key)
        }
        props[key]?.let {
            return props[key] as String
        }
        throw RuntimeException("Property ${key} marked as required but not present!")
    }fun readProperties():Properties = Properties().apply {
        FileInputStream("src/test/resources/testing.conf").use { fis ->
            load(fis)
        }
    }}//Kotlintest interceptors that fire before the entire suite, and after.
object GlobalTestSuiteInitializer : ProjectConfig() {private var started: Long = 0override fun beforeAll() {
        RestAssured.config = RestAssuredConfig.config().objectMapperConfig(ObjectMapperConfig.objectMapperConfig().jackson2ObjectMapperFactory(objectMapperFactory))
        started = System.currentTimeMillis()
    }override fun afterAll() {
        val time = System.currentTimeMillis() - started
        println("overall time [ms]: " + time)
    }
}
```

特别注意上面的*GlobalTestSuiteInitializer*，它可以用于任何种类的套件范围的引导。

**RestAssured 配置(RestAssuredSupport.kt)**

我们需要为 JSON 操作和其他事情设置一些繁琐的东西。

```
package app.util//…imports omitted…interface RestAssuredSupport {
    fun RequestSpecification.When(): RequestSpecification {
        return this.`when`()
    }

    fun ResponseSpecification.When(): RequestSender {
        return this.`when`()
    }}//[http://www.programcreek.com/java-api-examples/index.php?api=com.jayway.restassured.config.RestAssuredConfig](http://www.programcreek.com/java-api-examples/index.php?api=com.jayway.restassured.config.RestAssuredConfig)object ObjectMapperConfigurator {
    val objectMapper = ObjectMapper().registerModule(KotlinModule())
    init {
        objectMapper.configure(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES, false)
        objectMapper.configure(DeserializationFeature.FAIL_ON_MISSING_CREATOR_PROPERTIES, false)objectMapper.setSerializationInclusion(JsonInclude.Include.NON_EMPTY)
    }
    fun get():ObjectMapper {
        return objectMapper
    }
}val objectMapperFactory = object : Jackson2ObjectMapperFactory {override fun create(cls: Class<*>?, charset: String?): ObjectMapper {
        return ObjectMapperConfigurator.get()
    }
}
```

# 最后

格拉德试验

完成了。

如上所述——对我们来说真正重要的是，我们通过用户旅程和测试获得了用例覆盖(UCC ),从而在整个微服务中端到端地运行产品。这种用 Kotlin 测试 RESTful 微服务的方法将有助于我们实现这一目标。

[![](img/a9f346eff65776bdedf685617e2c446d.png)](https://medium.com/capital-one-tech/microservices/home)

***披露声明:以上观点为作者个人观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权都是其各自所有者的所有权。本文为 2017 首都一。***