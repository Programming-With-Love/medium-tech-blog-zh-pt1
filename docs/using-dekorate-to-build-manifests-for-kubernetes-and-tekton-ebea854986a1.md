# 使用 Dekorate 为 Kubernetes 和 Tekton 建立清单

> 原文：<https://medium.com/oracledevs/using-dekorate-to-build-manifests-for-kubernetes-and-tekton-ebea854986a1?source=collection_archive---------1----------------------->

![](img/348f73676b498158b1f23cb3e6b9a0af.png)

在[之前的一篇文章](https://lmukadam.medium.com/running-continuous-integration-on-oke-with-tekton-353684c15730)中，我们讨论了如何使用 Tekton 构建容器映像，以便将它们推送到 OCIR。本着同样的精神，本文将研究如何在另一个用例中使用 Tekton。

一旦构建了容器映像并将它们保存到容器注册中心，就需要部署它们。这通常是通过简单的 yaml 清单、添加了一些脚本功能的 helm charts、jsonner 或类似工具来完成的。

问题是，如果您正在部署大量的应用程序，那么您将需要编写大量的 YAMLs。肯定有更好的方法来做这些繁重的工作，对吗？事实证明，是有的。进入 [Dekorate](https://dekorate.io/) ，这是一个简洁的项目，提供了“Kubernetes 清单的生成器和装饰器的集合”。这将省去我们手写 Kubernetes 和 Tekton 货单的麻烦。

*注意:如果您还没有注册，您可以今天就* [*注册 Oracle 云免费层帐户*](https://signup.cloud.oracle.com/?language=en_US&sourceType=:ex:tb:::::RC_WWMK220210P00062:Medium_DekorateManifestKubernetes&SC=:ex:tb:::::RC_WWMK220210P00062:Medium_DekorateManifestKubernetes&pcode=WWMK220210P00062) *。*

首先，打开 pom.xml 并添加 dekorate 依赖项:

```
<dependency>
 <groupId>io.dekorate</groupId>
 <artifactId>kubernetes-spring-starter</artifactId
 <version>2.0.0.beta8</version>
</dependency>
<dependency>
 <groupId>io.dekorate</groupId>
 <artifactId>kubernetes-annotations</artifactId
 <version>2.0.0.beta8</version>
</dependency>
```

接下来，打开应用程序文件并添加以下导入和注释:

```
import io.dekorate.kubernetes.annotation.KubernetesApplication; import io.dekorate.kubernetes.annotation.Label;
import io.dekorate.kubernetes.annotation.Port;
import io.dekorate.kubernetes.annotation.Probe;
import io.dekorate.kubernetes.annotation.ResourceRequirements;
[@SpringBootApplicatio](http://twitter.com/SpringBootApplicatio)n
[@KubernetesApplicatio](http://twitter.com/KubernetesApplicatio)n(
 name = “helloworld”,
 labels = [@Label](http://twitter.com/Label)(key = “app”, value = “helloworld”),
 ports = [@Port](http://twitter.com/Port)(name = “http”, containerPort = 8080),
 livenessProbe = [@Probe](http://twitter.com/Probe)(httpActionPath = “/health/liveness”, 
 initialDelaySeconds = 5, timeoutSeconds = 3, failureThreshold = 10),
 readinessProbe = [@Probe](http://twitter.com/Probe)(httpActionPath = “/health/readiness”,
 initialDelaySeconds = 5, timeoutSeconds = 3, failureThreshold = 10),
 requestResources=[@ResourceRequirements](http://twitter.com/ResourceRequirements)(memory=”64Mi”, cpu=”1m”),
 limitResources=[@ResourceRequirements](http://twitter.com/ResourceRequirements)(memory=”256Mi”, cpu=”5m”)
)
```

您可以添加其他注释，但是现在，我们只对生成 Kubernetes 的清单感兴趣。

```
mvn clean package -DskipTests[INFO] --- maven-compiler-plugin:3.8.1:compile (default-compile) @ helloworld ---                                                                                                             
[INFO] Changes detected - recompiling the module!                                                                                                                                             
[INFO] Compiling 1 source file to D:\dev\java\helloworld\target\classes                                                                                                                       
[INFO] Found @KubernetesApplication on: net.midgard.helloworld.HelloworldApplication                                                                                                          
[INFO] Initializing dekorate session.                                                                                                                                                         
[INFO] Found @SpringBootApplication on: net.midgard.helloworld.HelloworldApplication                                                                                                          
[INFO] Generating manifests.                                                                                                                                                                  
[INFO] Processing kubernetes configuration.                                                                                                                                                   
[INFO] Writing: file:///D:/dev/java/helloworld/target/classes/META-INF/dekorate/kubernetes.json                                                                                               
**[INFO] Writing: file:///D:/dev/java/helloworld/target/classes/META-INF/dekorate/kubernetes.yml**                                                                                                
[INFO] Closing dekorate session.
```

如果您打开 kubernetes.yaml，您将看到 kubernetes 部署和服务对象已经创建:

```
---
apiVersion: v1
kind: Service
metadata:
  annotations:
    app.dekorate.io/vcs-url: [https://github.com/hyder/helloworld.git](https://github.com/hyder/helloworld.git)
    app.dekorate.io/commit-id: 816776b01175d5a8663ee9f74c05029477bd3eda
  labels:
    app: helloworld
    app.kubernetes.io/name: helloworld
    app.kubernetes.io/version: 0.0.1-SNAPSHOT
  name: helloworld
....
---
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    app.dekorate.io/vcs-url: [https://github.com/hyder/helloworld.git](https://github.com/hyder/helloworld.git)
    app.dekorate.io/commit-id: 816776b01175d5a8663ee9f74c05029477bd3eda
  labels:
    app.kubernetes.io/version: 0.0.1-SNAPSHOT
    app: helloworld
    app.kubernetes.io/name: helloworld
  name: helloworld
...
```

现在，您可以将这些文件复制到 Kubernetes 应用程序 infra git repo 中，并让 ArgoCD 之类的 CD 工具为您自动部署。

您还可以使用 Dekorate 为您生成 Tekton 任务、管道、任务运行和管道运行。只需将以下内容添加到 pom.xml:

```
<dependency>
  <groupId>io.dekorate</groupId>
  <artifactId>tekton-annotations</artifactId
  <version>2.0.0.beta8</version>
</dependency>
```

此时，如果您尝试打包，将会失败，并显示以下错误消息:

```
[ERROR] Failed to execute goal org.apache.maven.plugins:maven-compiler-plugin:3.8.1:compile (default-compile) on project helloworld: Fatal error compiling: java.lang.IllegalStateException: Project is not under version control, or unsupported version control system. Aborting generation of tekton resources! -> [Help 1]
```

那是因为我们还没有管理这个项目的版本。所以，让我们用 git 初始化它:

```
git init
git add .
git commit
```

Spring Initializr 已经在下载时在 zip 中添加了一个. gitignore 文件，所以我们现在不需要担心忽略任何东西。让我们再次尝试打包，我们将看到生成了 Kubernetes 和 Tekton 清单:

```
mvn clean package
…
[INFO] Writing: file:///D:/dev/java/helloworld/target/classes/META-INF/dekorate/kubernetes.json 
[INFO] **Writing: file:///D:/dev/java/helloworld/target/classes/META-INF/dekorate/kubernetes.yml** 
[INFO] Writing: file:///D:/dev/java/helloworld/target/classes/META-INF/dekorate/tekton-task-run.json 
[INFO] **Writing: file:///D:/dev/java/helloworld/target/classes/META-INF/dekorate/tekton-task-run.yml** 
[INFO] Writing: file:///D:/dev/java/helloworld/target/classes/META-INF/dekorate/tekton-pipeline-run.json 
[INFO] **Writing: file:///D:/dev/java/helloworld/target/classes/META-INF/dekorate/tekton-pipeline-run.yml** 
[INFO] Writing: file:///D:/dev/java/helloworld/target/classes/META-INF/dekorate/tekton-pipeline.json 
[INFO] **Writing: file:///D:/dev/java/helloworld/target/classes/META-INF/dekorate/tekton-pipeline.yml** 
[INFO] Writing: file:///D:/dev/java/helloworld/target/classes/META-INF/dekorate/tekton-task.json 
[INFO] **Writing: file:///D:/dev/java/helloworld/target/classes/META-INF/dekorate/tekton-task.yml**
```

我希望这篇文章对你有用。

# 加入对话！

如果你对 Oracle 开发人员在他们的自然环境中发生的事情感到好奇，请加入我们的公共休闲频道！我们不介意成为你的鱼缸🐠

由[本杰明·沃罗斯](https://unsplash.com/@vorosbenisop?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在 [Unsplash](https://unsplash.com/s/photos/sky-moon?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) 上拍摄的照片