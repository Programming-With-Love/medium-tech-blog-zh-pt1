# 基于 Java 模块系统、Helidon 和 Alpine 的 JDK 12 EA 的 Dockerized 应用程序

> 原文：<https://medium.com/oracledevs/dockerized-app-with-java-module-system-helidon-and-alpine-based-jdk-12-ea-53d9f240f58a?source=collection_archive---------0----------------------->

![](img/7b7df9720a96e84a2b5904b3a96779cb.png)

Photo by [frank mckenna](https://unsplash.com/@frankiefoto?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

如你所知，JDK 9 引入了一个*原生*模块系统来设计和构建应用程序，这些应用程序由一系列独立的模块组成，这些模块通过定义良好的公共接口相互通信。使用 Java 模块系统的一些优点是:

*   由于实现细节是隐藏的，因此增强了可维护性
*   可靠的配置，允许模块声明它们的依赖关系，并解决传统类路径机制的一些问题
*   能够构建特定于应用的定制运行时[映像](https://docs.oracle.com/en/java/javase/11/tools/jlink.html)
*   通过更快的启动时间和更少的内存占用，提高了平台的完整性和性能
*   由于强大的封装和减少平台和应用程序攻击面的潜力，间接提高了安全性

在容器集成和支持方面，JDK 版本 10 和 11 对 JVM 进行了改进，以确保 JVM 遵守容器上设置的内存和 CPU 限制。不仅如此，JDK 12 早期版本[现在包括了一个基于 Alpine Linux 的二进制文件，它使用了](https://jdk.java.net/12/) [musl](http://www.musl-libc.org/) C 库。使用基于 Alpine Linux 的二进制文件的最大优点是，现在可以构建更小的 docker 映像来运行 Java 应用程序。更小的图像大小反过来意味着我们可以获得更快的下载、更快的启动和减少安全漏洞的攻击面等好处。

让我们构建一个示例应用程序来看看 JDK 提供的这些强大功能。该应用程序是一个简单的静态图像服务器，使用 [Helidon](https://helidon.io) 构建，并作为 docker 容器部署。我们将使用 [Gradle](https://gradle.org/) 作为构建工具。

我们首先定义一个 gradle 构建脚本来设置 Java 模块系统和我们的应用程序的依赖项:

```
plugins {
    id "java-library"
    id "com.zyxist.chainsaw" version "0.3.1"
}repositories {
    mavenCentral()
}sourceCompatibility = "11"dependencies {
    compile "io.helidon.webserver:helidon-webserver:0.10.5"
    compile "io.helidon.webserver:helidon-webserver-netty:0.10.5"
}
```

由于我们使用本地 Java 模块系统，我们将创建一个“module-info”类来定义我们的模块:

```
module example.imageserver {
    exports example.imageserver;

    requires java.logging;
    requires io.helidon.webserver;
}
```

接下来，我们使用 Helidon 的内置静态内容处理程序创建一个静态图像服务器:

```
var contentSupport = StaticContentSupport
        .*create*(Paths.*get*("/app/images"));

var routing = Routing.*builder*()
        .register("/images", contentSupport)
        .build();

var config = ServerConfiguration.*builder*()
        .port(8080)
        .build();

WebServer.*create*(config, routing).start();
```

没错，就是用 Java 写的代码:-)。这就是创建一个静态 web 服务器的全部工作。现在让我们把注意力转向建立码头工人形象。我们将利用 palantir gradle [插件](https://github.com/palantir/gradle-docker)并配置它来构建我们的 docker 映像。以下是相关的 gradle 构建文件片段:

```
plugins {
    ...
    id 'com.palantir.docker' version "0.20.1"
}...docker {
    name "image-server:${version}"
    files configurations.compileClasspath, "${jar.archivePath}"
    copySpec.into("app")
}
```

接下来，我们定义“Dockerfile ”,它使用多阶段构建过程为我们的应用程序创建 docker 映像:

```
**FROM** alpine:latest as *build* # Check the JDK 12 EA downloads link to get the latest version
**RUN** mkdir **-**p **/**opt**/**jdk \
    **&&** wget **-**q "https://download.java.net/java/early_access/alpine/20/binaries/openjdk-12-ea+20_linux-x64-musl_bin.tar.gz" \
    **&&** tar **-**xzf "openjdk-12-ea+20_linux-x64-musl_bin.tar.gz" **-**C **/**opt**/**jdk

**RUN** ["/opt/jdk/jdk-12/bin/jlink", \
     "--compress=2", \
     "--strip-debug", \
     "--no-header-files", \
     "--no-man-pages", \
     "--module-path", "/opt/jdk/jdk-12/jmods", \
     "--add-modules", "java.base,java.logging,jdk.unsupported", \
     "--output", "/custom-jre"]

**FROM** alpine:latest
**COPY --**from=*build* **/**custom-jre **/**opt**/**jdk**/
ADD** app **/**app

**CMD** ["/opt/jdk/bin/java", \
     "--upgrade-module-path", "/app", \
     "-m", "examples.imageserver/examples.imageserver.Server"]
```

注意我们如何使用 docker 构建过程的第一阶段来下载基于 Alpine Linux 的 JDK，并使用 jlink 创建一个定制的 JRE。对于自定义 JRE，除了添加“java.base”模块和“java.logging”模块，我们还添加了“jdk.unsupported”模块，以允许 netty(支持 Helidon)访问内部 jdk 类。

docker 构建的第二阶段将以 Alpine Linux 作为基础映像开始(大约 4.5 MB)。然后，我们添加在第一阶段构建的定制 JRE 以及我们的应用程序模块。最后一步是添加运行应用程序的命令。有了这些文件和配置，是时候构建我们的应用程序并将其打包为 docker 映像了。让我们请格雷尔为我们做这件事:

```
gradlew clean build docker
```

上面的命令将构建一个 docker 映像，它只包含我们的定制 JRE 以及应用程序 jar。如果你现在运行“docker images ”,你会发现我们的 docker images 只有 58.6 MB 大小。这不是很好吗？我们现在可以将应用程序作为 docker 容器运行:

```
docker run -d -p 8080:8080 imageserver
```

使用浏览器，导航到[http://localhost:8080/images](http://localhost:8080/images/container-3.jpg)，查看我们的 dockerized 应用程序的运行情况。

您可以前往 [Github](https://github.com/udaychandra/image-server) 来克隆和使用示例项目。