# 如何在 Azure App Service 上使用 Java 客户端库构建操作

> 原文：<https://medium.com/google-developer-experts/how-to-build-actions-with-java-client-library-on-azure-app-service-3df7e6b1aa0b?source=collection_archive---------6----------------------->

最后，谷歌发布了一个新的客户端库。名字是“Google Java/Kotlin 客户端库上的动作”。

[](https://github.com/actions-on-google/actions-on-google-java) [## google 上的操作/Google-Java 上的操作

### 用于在 Google 上操作的 Java/Kotlin 库。通过以下方式为 actions-on-Google/actions-on-Google-Java 开发做出贡献…

github.com](https://github.com/actions-on-google/actions-on-google-java) 

到目前为止，大多数开发人员可以使用一个名为“Node.js 的 Google 客户端库上的操作”的客户端库。也就是说，我们只有用 Node.js 构建动作的方法。但是，目前，我们可以使用 Java/Kotlin 客户端库轻松地用 Java 编程语言构建动作。

在以下文章中描述了用法摘要:

[](/google-developers/announcing-the-java-kotlin-client-library-for-actions-on-google-217828dead) [## 宣布 Java & Kotlin 客户端库可以在 Google 上运行

### 自从平台首次推出以来，我们已经为 Google 上的操作支持了 Node.js 客户端库。我们学到了很多…

medium.com](/google-developers/announcing-the-java-kotlin-client-library-for-actions-on-google-217828dead) 

在本文中，我将介绍如何用 Java/Kotlin 客户端库构建动作，并将它们部署到 Azure App 服务。

![](img/254bf967780bdbefb2a28957b64ff0d9.png)

# 先决条件

您需要准备以下内容:

*   Java 开发工具包 1.8 版或更高版本。
*   [Maven](https://maven.apache.org/) 版本 3.6.0 以上。
*   您已经在 Azure 上注册了您的帐户。
*   您已经安装了 [Azure CLI](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest) 并登录到 Azure。
*   你已经了解了如何在谷歌云平台或亚马逊网络服务上为谷歌助手构建动作。
*   您已经在 Google project 上有了您的操作，并且您的 Dialogflow 代理已经连接到 AoG 项目。

如果你不知道如何构建动作，你可以用 codelabs 来研究一下:

[](https://codelabs.developers.google.com/codelabs/actions-1/index.html#0) [## 为 Google Assistant 构建操作(第 1 级)

### 这个 codelab 是多模块教程的一部分。每个模块可以单独学习，也可以按照学习顺序学习…

codelabs.developers.google.com](https://codelabs.developers.google.com/codelabs/actions-1/index.html#0) [](https://codelabs.developers.google.com/codelabs/actions-2/index.html#0) [## 为 Google Assistant 构建操作(第 2 级)

### 此 codelab 模块是多模块教程的一部分。每个模块可以单独学习，也可以按学习顺序学习…

codelabs.developers.google.com](https://codelabs.developers.google.com/codelabs/actions-2/index.html#0) 

此外，您需要使用 Azure CLI 登录 Azure。如果您尚未登录，请执行以下命令:

```
$ az login
$ az account set --subscription <SUBSCRIPTION_ID>
```

# 创建您的资源组

让我们开始创建您的行动。首先，使用以下命令创建一个新的资源组:

```
$ az group create --name <RESOURCE_GROUP_NAME> --location <LOCATION_NAME>
```

# 创建项目

让我们开始构建您的操作。首先，用 Maven 创建您的新项目。在本文中，我们使用“simple-action”作为项目名，使用“jp.eisbahn . actions . simple action”作为包名(“jp . EIS Bahn”是我拥有的域)。

```
$ mvn archetype:generate -DgroupId=jp.eisbahn.actions.simpleaction -DartifactId=simple-action -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false
```

在执行上面的命令之后，您有了`simple-action`目录，并且在该目录中生成了一些文件。但是有些文件对于本文来说是不必要的。删除下列文件和目录:

*   `src/main/webapp/WEB-INF/web.xml`
*   `src/main/webapp/index.jsp`

# 编辑 pom.xml 文件

创建项目后，用以下内容替换`pom.xml`文件:

```
<project ae ly" href="http://maven.apache.org/POM/4.0.0" rel="noopener ugc nofollow" target="_blank">http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="[http://www.w3.org/2001/XMLSchema-instance](http://www.w3.org/2001/XMLSchema-instance)" xsi:schemaLocation="[http://maven.apache.org/POM/4.0.0](http://maven.apache.org/POM/4.0.0) [http://maven.apache.org/maven-v4_0_0.xsd](http://maven.apache.org/maven-v4_0_0.xsd)">
  <modelVersion>4.0.0</modelVersion>
  <groupId>jp.eisbahn.actions.simpleaction</groupId>
  <artifactId>simple-action</artifactId>
  <packaging>war</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>simple-action Maven Webapp</name>
  <url>[http://maven.apache.org](http://maven.apache.org)</url>
  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <java.version>1.8</java.version>
  </properties> <dependencies>
    <dependency>
      <groupId>javax.servlet</groupId>
      <artifactId>javax.servlet-api</artifactId>
      <version>3.1.0</version>
      <scope>provided</scope>
    </dependency>
    <dependency>
      <groupId>com.google.actions</groupId>
      <artifactId>actions-on-google</artifactId>
      <version>1.0.1</version>
    </dependency>
  </dependencies> <build>
    <finalName>simple-action</finalName> <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>3.8.0</version>
        <configuration>
          <source>${java.version}</source>
          <target>${java.version}</target>
        </configuration>
      </plugin> <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-war-plugin</artifactId>
        <version>3.2.2</version>
        <configuration>
          <failOnMissingWebXml>false</failOnMissingWebXml>
        </configuration>
      </plugin> <plugin>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-webapp-maven-plugin</artifactId>
        <version>1.5.2</version>
        <configuration>
          <schemaVersion>v2</schemaVersion>
          <resourceGroup>__RESOURCE_GROUP_NAME__</resourceGroup>
          <appName>__APP_NAME__</appName>
          <region>__LOCATION_NAME__</region>
          <pricingTier>__PRICE_TEAR_NAME__</pricingTier>
          <runtime>
            <os>windows</os>
            <javaVersion>1.8</javaVersion>
            <webContainer>tomcat 8.5</webContainer>
          </runtime>
          <deployment>
            <resources>
              <resource>
                <directory>${project.basedir}/target</directory>
                <includes>
                  <include>*.war</include>
                </includes>
              </resource>
            </resources>
          </deployment>
        </configuration>
      </plugin>
    </plugins>
  </build>
</project>
```

在上面的代码中，`[azure-webapp-maven-plugin](https://docs.microsoft.com/en-us/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme?view=azure-java-stable)`插件元素有一些占位符。您需要替换以下占位符:

*   `__RESOURCE_GROUP_NAME__`:您创建的资源组名称。
*   `__APP_NAME__`:您的应用程序的标识符。
*   `__LOCATION_NAME__`:您要创建 app 的位置名称。
*   `__PRICING_TIER_NAME__`:定价层级[名称](https://docs.microsoft.com/en-us/java/api/overview/azure/maven/azure-webapp-maven-plugin/readme?view=azure-java-stable#pricingtier)。

如果不存在，则`azure-webapp-maven-plugin`会自动生成新的应用服务。现在就决定他们的名字，换掉他们。

## 对于 Java 开发工具包版本 11

如果您使用的是 Java Development Kit 版本 11 或更高版本，您将会在使用上面的`pom.xml`文件执行`mvn`命令时看到一个错误。原因是 JAXB 被弃用了。要解决这个问题，您需要向`azure-webapp-maven-plugin`插件元素添加以下定义:

```
<plugin>
  <groupId>com.microsoft.azure</groupId>
  <artifactId>azure-webapp-maven-plugin</artifactId>
  <version>1.5.2</version>
  <configuration>
    ...
  </configuration> <!-- Add the following definition -->
  <dependencies>
    <dependency>
      <groupId>javax.xml.bind</groupId>
      <artifactId>jaxb-api</artifactId>
      <version>2.4.0-b180830.0359</version>
    </dependency>
    <dependency>
      <groupId>com.sun.xml.bind</groupId>
      <artifactId>jaxb-impl</artifactId>
      <version>2.4.0-b180830.0438</version>
    </dependency>
    <dependency>
      <groupId>com.sun.xml.bind</groupId>
      <artifactId>jaxb-core</artifactId>
      <version>2.3.0.1</version>
    </dependency>
    <dependency>
      <groupId>javax.activation</groupId>
      <artifactId>activation</artifactId>
      <version>1.1.1</version>
    </dependency>
  </dependencies>
</plugin>
```

# 创建履行代码

接下来，您需要为您的操作创建履行代码。使用以下命令创建源目录:

```
$ mkdir -p src/main/java/jp/eisbahn/actions/simpleaction
```

然后，在`simpleaction`目录中创建名为“SimpleApp.java”的实现文件，内容如下:

```
package jp.eisbahn.actions.simpleaction;import com.google.actions.api.ActionRequest;
import com.google.actions.api.ActionResponse;
import com.google.actions.api.DialogflowApp;
import com.google.actions.api.ForIntent;
import com.google.actions.api.response.ResponseBuilder;public class SimpleApp extends DialogflowApp { @ForIntent("Default Welcome Intent")
    public ActionResponse welcome(ActionRequest request) {
        ResponseBuilder builder = getResponseBuilder(request);
        builder.add("Hello, Azure App Service!");
        return builder.build();
    }}
```

在上面的代码中，`SimpleApp`类扩展了`DialogflowApp`类。也就是说，该类可以处理来自 Dialogflow 代理的请求。并且，您可以使用`ForIntent`注释创建意图处理程序方法。

第一个参数包含请求的信息。并且，您可以用`ResponseBuilder`类创建`ActionResponse`实例。

 [## 响应生成器

### 构建器来组合来自动作 webhook 的响应。推荐的方法是从…获取 ResponseBuilder 实例

google.github.io 上的操作](https://actions-on-google.github.io/actions-on-google-java/com/google/actions/api/response/ResponseBuilder.html) 

通过阅读上面的 API 参考，您可以知道如何构建响应。

# 创建 Servlet 代码

顺便说一下，`SimpleApp`类没有任何处理 HTTP 请求的能力。相反，您需要创建一个处理程序类来处理请求和响应。在本文中，创建一个 Java Servlet 代码来处理 HTTP 请求和响应。

在`simpleaction`目录中创建名为“SimpleServlet.java”的实施文件，内容如下:

```
package jp.eisbahn.actions.simpleaction;import com.google.actions.api.App;
import javax.servlet.annotation.WebServlet;
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;
import java.util.Collections;
import java.util.Map;
import java.util.concurrent.ExecutionException;
import java.util.stream.Collectors;@WebServlet(name = "SimpleServlet", urlPatterns = {"/"}, loadOnStartup = 1)
public class SimpleServlet extends HttpServlet {private App app = new SimpleApp(); @Override
    protected void doPost(
            HttpServletRequest req, HttpServletResponse resp)
            throws IOException {
        String requestBody =
            req.getReader().lines().collect(Collectors.joining());
        Map<String, String> headersMap =
            Collections.list(req.getHeaderNames())
                .stream()
                .collect(Collectors.toMap(
                    name -> name,
                    req::getHeader));
        try {
            String responseBody =
                app.handleRequest(requestBody, headersMap).get();
            resp.setContentType("application/json; charset=utf-8");
            resp.getWriter().write(responseBody);
        } catch (InterruptedException | ExecutionException e) {
            e.printStackTrace();
            resp.setStatus(
                HttpServletResponse.SC_INTERNAL_SERVER_ERROR);
            resp.getWriter().write(e.getMessage());
        }
    }}
```

要调用`SimpleApp`实例，您需要以下信息:

*   请求正文的字符串。
*   拥有所有请求头条目的`Map<String, String>`对象。

然后，用这些参数调用`SimpleApp`实例的`handleRequest()`方法。如果调用方法的结果是成功，则返回`CompletableFuture<String>`对象。这是针对 JavaScript 中的异步 like Promise。在上面的代码中，通过调用`get()`方法来检索实际结果。

结果是响应的字符串值，表示为 JSON 字符串。`SimpleServlet`发送带有通过调用`getWriter()`方法检索的 Writer 对象的响应字符串。

# 部署您的代码

干得好！你有所有必要的文件。现在，您可以将您的操作部署到 Azure。

执行以下命令来部署您的操作:

```
$ mvn package azure-webapp:deploy
```

如果您看到如下输出，则部署成功。

```
[INFO] Scanning for projects...
[INFO]
[INFO] -----------< jp.eisbahn.actions.simpleaction:simple-action >------------
[INFO] Building simple-action Maven Webapp 1.0-SNAPSHOT
[INFO] --------------------------------[ war ]---------------------------------
[INFO]
...
[INFO] Deploying the war file simple-action.war...
[INFO] Successfully deployed the artifact to [https://<APP_NAME>.azurewebsites.net](https://yoichirosimpleaction.azurewebsites.net)
[INFO] ------------------------------------------------------------------------
[INFO] BUILD SUCCESS
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  25.064 s
[INFO] Finished at: 2019-01-23T15:57:41+09:00
[INFO] ------------------------------------------------------------------------
```

您可以通过以下命令对其进行测试:

```
$ curl -v -X POST -d "{}" https://<APP_NAME>.azurewebsites.net/
```

如果您收到如下输出，就没问题了:

```
< HTTP/1.1 500  
< Content-Length: 55
< X-Powered-By: ASP.NET
< Set-Cookie: ARRAffinity=932c5351a290b46f876f6d4453f2bdc8625e8682cbfa134e04eb88e0677cb1fc;Path=/;HttpOnly;Domain=<APP_NAME>.azurewebsites.net
< Date: Wed, 23 Jan 2019 07:02:34 GMT
< 
* Connection #0 to host <APP_NAME>.azurewebsites.net left intact
java.lang.Exception: Intent handler not found - INVALID%
```

Google Java/Kotlin 客户端库上的操作返回了消息“未找到意图处理程序”。

部署后，在您的 Dialogflow 代理上将该 URL 注册为 fulfillment webhook URL。然后，在动作模拟器上调用您的动作。你应该得到回应“你好，Azure 应用服务！”从谷歌助手。

# 结论

如果你是一名 Java 程序员，你可以用 Google Java/Kotlin 客户端库上的动作和你所熟悉的 Java 语言为 Google Assistant 构建你的动作。当你对谷歌助手的操作感兴趣时，如果这篇文章对你有用，我会很高兴。

最后，您可以从下面的 GitHub 库获得完整的代码集:

[](https://github.com/yoichiro/simple-action-azure-java-maven) [## 洋一郎/简单-动作-azure-java-maven

### 这是一个简单的项目，为部署到 Azure 应用服务的谷歌动作建立一个实现…

github.com](https://github.com/yoichiro/simple-action-azure-java-maven)