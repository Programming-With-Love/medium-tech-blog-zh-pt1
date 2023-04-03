# 如何在 Google AppEngine 上用 Java 客户端库构建动作

> 原文：<https://medium.com/google-developer-experts/how-to-build-actions-with-java-client-library-on-google-appengine-2eef7a5bd402?source=collection_archive---------5----------------------->

上周，发布了一个新的客户端库。名称是“对 Google Java/Kotlin 客户端库的操作”。

[](https://github.com/actions-on-google/actions-on-google-java) [## google 上的操作/Google-Java 上的操作

### 用于在 Google 上操作的 Java/Kotlin 库。通过以下方式为 actions-on-Google/actions-on-Google-Java 开发做出贡献…

github.com](https://github.com/actions-on-google/actions-on-google-java) 

到目前为止，大多数开发人员可以使用一个名为“Node.js 的 Google 客户端库上的操作”的客户端库。也就是说，我们只有用 Node.js 构建动作的方法。但是，目前，我们可以使用 Java/Kotlin 客户端库轻松地用 Java 编程语言构建动作。

在以下文章中描述了用法摘要:

[](/google-developers/announcing-the-java-kotlin-client-library-for-actions-on-google-217828dead) [## 宣布 Java & Kotlin 客户端库可以在 Google 上运行

### 自从平台首次推出以来，我们已经为 Google 上的操作支持了 Node.js 客户端库。我们学到了很多…

medium.com](/google-developers/announcing-the-java-kotlin-client-library-for-actions-on-google-217828dead) 

在本文中，我将介绍如何使用 Java/Kotlin 客户端库构建动作，并将其部署到 Google AppEngine。

![](img/10cb7f367d1edd72efc2149b953d08d5.png)

# 先决条件

在构建操作之前，您需要安装以下组件:

*   Java 开发工具包 1.8 版或更高版本。
*   [Gradle](https://gradle.org/install/) 版本 5.1.1 或更高。
*   [谷歌云 SDK](https://cloud.google.com/sdk/docs/)
*   App Engine SDK for Java(您可以使用以下命令安装它)

```
gcloud components install app-engine-java
```

*   您已经在 Google project 上有了您的操作，并且您的 Dialogflow 代理已经连接到 AoG 项目。

此外，你有一个谷歌云平台上的项目，其中的计费已经启用。

如果你不知道如何构建动作，你可以用 codelabs 来研究一下:

[](https://codelabs.developers.google.com/codelabs/actions-1/index.html#0) [## 为 Google Assistant 构建操作(第 1 级)

### 这个 codelab 是多模块教程的一部分。每个模块可以单独学习，也可以按照学习顺序学习…

codelabs.developers.google.com](https://codelabs.developers.google.com/codelabs/actions-1/index.html#0) [](https://codelabs.developers.google.com/codelabs/actions-2/index.html#0) [## 为 Google Assistant 构建操作(第 2 级)

### 此 codelab 模块是多模块教程的一部分。每个模块可以单独学习，也可以按学习顺序学习…

codelabs.developers.google.com](https://codelabs.developers.google.com/codelabs/actions-2/index.html#0) 

# 创建项目

让我们开始构建您的操作。首先，用 Gradle 创建你的新项目。在本文中，我们使用“simple-fulfillment-java”作为项目名，使用“jp.eisbahn . actions . simple action”作为包名(“jp . EIS Bahn”是我拥有的域)。

```
$ mkdir simple-fulfillment-appengine
$ cd simple-fulfillment-appengine
$ gradle init
```

`gradle`命令会问你一些问题。用下面的话回答他们:

*   选择要生成的项目类型:1(基本)
*   选择构建脚本 DSL: 1 (groovy)
*   项目名称:“简单实现 java”

生成包括`build.gradle`文件在内的一些文件。

# 编辑构建文件

创建您的项目后，`build.gradle`文件是空的。将其替换为以下内容:

```
buildscript {
    repositories {
        jcenter()
        mavenCentral()
    }
    dependencies {
        classpath 'com.google.cloud.tools:appengine-gradle-plugin:1.+'
    }
}repositories {
    jcenter()
    mavenCentral()
}apply plugin: 'java'
apply plugin: 'war'
apply plugin: 'com.google.cloud.tools.appengine'dependencies {
    compile 'com.google.appengine:appengine-api-1.0-sdk:+'
    compile group: 'com.google.actions', name: 'actions-on-google', version: '1.0.0'
    providedCompile 'javax.servlet:javax.servlet-api:3.1.0'
}appengine {
    deploy {
    }
}group = 'jp.eisbahn.actions.simpleaction'
version = '1.0-SNAPSHOT'sourceCompatibility = '1.8'
targetCompatibility = '1.8'
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
        builder.add("Hello, Google AppEngine!");
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

在`simpleaction`目录中创建名为“SimpleServlet.java”的实现文件，内容如下:

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
public class SimpleServlet extends HttpServlet { private App app = new SimpleApp(); @Override
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

然后，用这些参数调用`SimpleApp`实例的`handleRequest()`方法。如果调用方法的结果为成功，则返回`CompletableFuture<String>`对象。这是针对 JavaScript 中的异步 like Promise。在上面的代码中，通过调用`get()`方法来检索实际结果。

结果是响应的字符串值，表示为 JSON 字符串。`SimpleServlet`发送带有通过调用`getWriter()`方法检索的 Writer 对象的响应字符串。

# 为 AppEngine 创建配置文件

最后，您需要为 AppEngine 准备一个配置文件。通过以下命令创建一个放置文件的新目录:

```
$ mkdir -p src/main/webapp/WEB-INF
```

然后，在`WEB-INF`目录中创建以下名为“appengine-web.xml”的文件:

```
<?xml version="1.0" encoding="UTF-8"?>
<appengine-web-app ae ly" href="http://appengine.google.com/ns/1.0" rel="noopener ugc nofollow" target="_blank">http://appengine.google.com/ns/1.0">
    <runtime>java8</runtime>
    <threadsafe>true</threadsafe>
</appengine-web-app>
```

# 将您的代码部署到 AppEngine

好了，您创建了所有文件。现在，您可以将代码部署到 AppEngine。首先，如果选择了其他项目，您需要指定要部署代码的目标项目。

```
$ gcloud config set project <YOUR_PROJECT_ID>
```

让我们通过以下命令部署您的代码:

```
$ gradle appengineDeploy
```

如果部署成功，您可以通过以下命令对其进行测试:

```
$ curl -v -X POST -d "{}" https://<PROJECT_ID>.appspot.com/
```

如果您收到如下输出，就没问题了:

```
< HTTP/2 500 
< x-cloud-trace-context: 4ba6987ee3351d91cd43fff194b79962;o=1
< date: Mon, 21 Jan 2019 00:36:39 GMT
< content-type: text/html
< server: Google Frontend
< content-length: 55
< alt-svc: quic=":443"; ma=2592000; v="44,43,39,35"
< 
* Curl_http_done: called premature == 0
* Connection #0 to host azure-test-65d3c.appspot.com left intact
java.lang.Exception: Intent handler not found - INVALID%
```

Google Java/Kotlin 客户端库上的操作返回了消息“未找到意图处理程序”。

部署后，在您的 Dialogflow 代理上将该 URL 注册为 fulfillment webhook URL。然后，在动作模拟器上调用您的动作。你应该得到这样的回应“你好，Google AppEngine！”从谷歌助手。

# 结论

如果你是一名 Java 程序员，你可以用 Google Java/Kotlin 客户端库上的动作和你所熟悉的 Java 语言为 Google Assistant 构建你的动作。当你对谷歌助手的操作感兴趣时，如果这篇文章对你有用，我会很高兴。

最后，您可以从下面的 GitHub 库获得完整的代码集:

[](https://github.com/yoichiro/simple-fulfillment-appengine) [## 洋一郎/简单实现 appengine

### 这是一个简单的项目，为部署到 Google AppEngine Java 的 Google 上的操作建立一个实现…

github.com](https://github.com/yoichiro/simple-fulfillment-appengine)