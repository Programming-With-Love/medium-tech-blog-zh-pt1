# Spring vs Spring Boot:这些 Java 框架的比较

> 原文：<https://medium.com/quick-code/spring-vs-spring-boot-a-comparison-of-these-java-frameworks-14a1b594657?source=collection_archive---------0----------------------->

## 想进一步了解这两个流行的 Java 框架吗？看看这篇文章，它们是如何解决不同类型的问题的。

![](img/916374ffe3dd393ac0368bf9f2509377.png)

什么是 Spring Boot？什么是 Spring 框架？他们的目标是什么？怎么比较呢？你脑子里一定有很多疑问。在这篇博客的结尾，你会有所有这些问题的答案。随着对 Spring 和 Spring Boot 框架的了解越来越多，你会逐渐理解它们各自解决不同类型的问题。更多关于 [**Spring Boot 在线培训**](https://onlineitguru.com/spring-boot-training.html) 的附加信息

# 春天是什么？Spring 解决的核心问题是什么？

Spring 框架是最流行的 Java 应用程序开发框架之一。Spring 最好的特性之一是它有**依赖注入(DI)** 或**控制反转(IOC)**，这允许我们开发松散耦合的应用程序。而且，松散耦合的应用程序可以很容易地进行单元测试。

# 没有依赖注入的例子

考虑下面的例子— `MyController`依赖于`MyService`来执行某个任务。因此，要获得 MyService 的实例，我们将使用:

`MyService service = new MyService();`

现在，我们已经为`MyService`创建了实例，我们看到两者是紧密耦合的。如果我在`MyController`的单元测试中为`MyService`创建一个模拟，我如何让`MyController`使用这个模拟？这有点难，不是吗？

```
@RestController
public class MyController {
    private MyService service = new MyService(); @RequestMapping("/welcome")
   public String welcome() {
        return service.retrieveWelcomeMessage();
    }}
```

# 依赖注入的例子

只需要借助两个注释，我们就可以很容易地得到`MyService`的实例，它不是紧耦合的。Spring 框架做了所有艰苦的工作来使事情变得更简单。

*   `@Component`只是在 Spring 框架中用作*一个 bean，您需要在自己的 BeanFactory(工厂模式的一个实现)中管理它。*
*   `@Autowired`只是用来在 Spring 框架中为这个特定的类型找到正确的匹配并自动连接它。

因此，Spring framework 将为`MyService`创建一个 bean，并将其自动绑定到`MyController`中。

在单元测试中，我可以要求 Spring 框架将`MyService`的模拟自动连接到`MyController`。

```
@Component
public class MyService {
    public String retrieveWelcomeMessage(){
        return "Welcome to InnovationM";
  }}@RestController
public class MyController { @Autowired
    private MyService service; @RequestMapping("/welcome")
    public String welcome() {
        return service.retrieveWelcomeMessage();
    }}
```

Spring 框架还有很多其他特性，分为二十个模块来解决很多常见问题。以下是一些更受欢迎的模块:

*   春天的 JDBC
*   Spring MVC
*   春季 AOP
*   弹簧 ORM
*   Spring JMS
*   弹簧试验
*   Spring 表达式语言

面向方面编程(AOP)是 Spring 框架的另一个优势。面向对象编程中的关键单元是**类**，而在 AOP 中，关键单元是**方面**。例如，如果您想在项目中添加安全性、日志记录等。，您可以只使用 AOP，将这些作为横切关注点，远离您的主要业务逻辑。您可以在方法调用之后、方法调用之前、方法返回之后或异常出现之后执行任何操作。

Spring 框架没有自己的 ORM，但是它提供了与 ORM 非常好的集成，像 Hibernate、Apache iBATIS 等等。

简而言之，我们可以说 Spring 框架提供了一种开发 web 应用程序的解耦方式。借助 Spring 中的这些概念，如 Dispatcher Servlet、ModelAndView 和 View Resolver，Web 应用程序开发变得很容易。

# 如果春天能解决这么多问题，我们为什么还需要 Spring Boot？

现在，如果你已经使用过 Spring，想想你在开发一个功能齐全的 Spring 应用程序时遇到的问题。想不出一个？让我告诉你——设置 Hibernate 数据源、实体管理器、会话工厂和事务管理有很多困难。开发人员使用 Spring MVC 建立一个功能最少的基本项目需要花费大量时间。

> 获得 [**Spring Boot 认证**](https://onlineitguru.com/spring-boot-training.html) ，将您的职业生涯推向新的成功高度

```
<bean
          class="org.springframework.web.servlet.view.InternalResourceViewResolver">
    <property name="prefix">
         <value>/WEB-INF/views/</value>
    </property>
    <property name="suffix">
         <value>.jsp</value>
    </property></bean>
<mvc:resources mapping="/webjars/**" location="/webjars/"/> <servlet>
      <servlet-name>dispatcher</servlet-name>
      <servlet-class>
           org.springframework.web.servlet.DispatcherServlet
      </servlet-class>
      <init-param> 
           <param-name>contextConfigLocation</param-name>
           <param-value>/WEB-INF/my-servlet.xml</param-value>
      </init-param>
      <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
      <servlet-name>dispatcher</servlet-name>
      <url-pattern>/</url-pattern>
</servlet-mapping>
```

当我们使用 Hibernate 时，我们必须配置像 datasource、EntityManager 等这样的东西。

```
<bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource"
         destroy-method="close">
         <property name="driverClass" value="${db.driver}" />
         <property name="jdbcUrl" value="${db.url}" />
         <property name="user" value="${db.username}" />
         <property name="password" value="${db.password}" />
</bean>
<jdbc:initialize-database data-source="dataSource">
         <jdbc:script location="classpath:config/schema.sql" />
         <jdbc:script location="classpath:config/data.sql" />
</jdbc:initialize-database>
<bean
 class="org.springframework.orm.jpa.LocalContainerEntityManagerFactoryBean"
         id="entityManagerFactory">
         <property name="persistenceUnitName" value="hsql_pu" /
          <property name="dataSource" ref="dataSource" />
</bean>
<bean id="transactionManager" class="org.springframework.orm.jpa.JpaTransactionManager">
          <property name="entityManagerFactory" ref="entityManagerFactory" />
          <property name="dataSource" ref="dataSource" />
</bean>
<tx:annotation-driven transaction-manager="transactionManager"/>
```

# Spring Boot 是如何解决这个问题的？

1.  Spring Boot 使用`AutoConfiguration`完成所有这些工作，并且会处理你的应用程序需要的所有内部依赖——你需要做的就是运行你的应用程序。如果 Spring `jar`在类路径中，Spring Boot 将自动配置 Dispatcher Servlet。如果 Hibernate `jar`在类路径中，它将自动配置到数据源。Spring Boot 给了我们一组预配置的启动项目，作为我们项目的一个依赖项。
2.  在 web 应用程序开发期间，我们需要我们想要使用的 jar，使用 jar 的哪个版本，以及如何将它们连接在一起。所有的 web 应用程序都有类似的需求，例如，Spring MVC、Jackson Databind、Hibernate core 和 Log4j(用于日志记录)。因此，我们必须选择所有这些罐子的兼容版本。为了降低复杂性，Spring Boot 引入了我们所谓的 **Spring Boot 首发。**

# Spring Web 项目的依赖项

```
<dependency>
     <groupId>org.springframework</groupId>
     <artifactId>spring-webmvc</artifactId
     <version>4.2.2.RELEASE</version>
</dependency>
<dependency>
     <groupId>com.fasterxml.jackson.core</groupId
     <artifactId>jackson-databind</artifactId
     <version>2.5.3</version>
</dependency>
<dependency>
     <groupId>org.hibernate</groupId>
     <artifactId>hibernate-validator</artifactId
     <version>5.0.2.Final</version>
</dependency>
<dependency>
     <groupId>log4j</groupId>
     <artifactId>log4j</artifactId>
     <version>1.2.17</version>
</dependency>
```

**启动器** *是一组方便的依赖项，您可以将它们包含在您的 Spring Boot 应用程序中。为了使用 Spring 和 Hibernate，我们只需要在项目中包含 spring-boot-starter-data-jpa 依赖项。*

# Spring Boot Starter Web 的依赖项

```
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
</dependency>
```

下面的屏幕截图显示了添加到我们的应用程序中的单个依赖项下的不同包:

![](img/e11082088d1ba6d89ba1dfad2071badf.png)

你还会看到其他的包。一旦您添加了 starter 依赖项，Spring Boot Starter Web 就会预打包所有这些内容。作为开发人员，我们不需要担心这些依赖项和它们的兼容版本。

# Spring Boot 启动项目选项

这些是帮助我们快速开始开发特定类型的应用程序的几个入门项目。

*   spring-boot-starter-Web-Services:SOAP Web 服务
*   web: Web 和 RESTful 应用程序
*   弹簧-启动-启动-测试:单元测试和集成测试
*   jdbc:传统的 JDBC
*   spring-boot-starter-hateoas:为您的服务添加 hateoas 特性
*   spring-boot-starter-security:使用 Spring Security 进行认证和授权
*   带 Hibernate 的 Spring Data JPA
*   spring-boot-starter-cache:启用 spring 框架的缓存支持
*   spring-boot-starter-data-rest:使用 Spring Data rest 公开简单的 REST 服务