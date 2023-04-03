# AWS Lambda Java 教程:降低冷启动的最佳实践

> 原文：<https://medium.com/capital-one-tech/aws-lambda-java-tutorial-best-practices-to-lower-cold-starts-capital-one-dc1d8806118?source=collection_archive---------5----------------------->

## 从海龟到飞人

![](img/042b627e98b31c8dc31cde3ae8e87efc.png)

海龟经常被认为是动物王国中行动缓慢的人，作为一名云端 Java 工程师，我感觉自己与这些动物有某种精神上的联系。加入我的旅程，我们将带着我们的 AWS Lambda Java 函数从 [*【伯蒂】【超速王】乌龟*](https://www.youtube.com/watch?v=i6nYWsXnl6M) *到* [*Zond 5 乌龟*](https://www.discovermagazine.com/the-sciences/the-first-earthlings-around-the-moon-were-two-soviet-tortoises) *。*

嗨，我是肖恩·奥图尔。我是英国第一资本公司的首席软件工程师。我在 2019 年底参加了 AWS re:Invent，学到了很多与我在云中工作的后端工程师角色相关的有趣的事情。我将写一系列关于我在 re:Invent 中学到的关于云工程实践的东西的帖子，希望将这些最佳实践灌输给 Java 社区。在第一部分 AWS Lambda Java 教程——中，我们将探讨 AWS Lambda 和 Java 的最佳实践，以帮助提高冷启动时的性能。

AWS 工程师已经做了很多工作来帮助解决这个问题，例如[供应并发](https://aws.amazon.com/blogs/aws/new-provisioned-concurrency-for-lambda-functions/)，但是我们仍然应该努力编写更多适合云的代码来获得我们可以获得的性能。在这篇 AWS Lambda 教程中，我们将会看到 11 个最佳实践，当我们在 Lambda 这样的资源受限环境中工作时，这些实践会使我们的 Java 代码更适合云。这包括减少依赖性，利用更多 lambda 友好的库，减少反射，以及一些特定于 lambda 的技巧来获得更好的性能。所以，事不宜迟，让我们直接开始把你的应用从 turtle 变成 hurtle。

# Java 和 AWS Lambda 有什么问题？

lambda **里的 Java 可以**慢。众所周知，Java 在 AWS Lambda 冷启动执行时间方面有一段特别艰难的时间，这是 AWS 博客中的[所涉及的内容，也是](https://aws.amazon.com/blogs/opensource/java-apis-aws-lambda)[广泛发布的](https://levelup.gitconnected.com/aws-lambda-cold-start-language-comparisons-2019-edition-%EF%B8%8F-1946d32a0244) [其他地方发布的](https://hackernoon.com/cold-starts-in-aws-lambda-f9e3432adbf0)。这种行为甚至在控制诸如在 VPC 中运行时创建弹性网络接口之类的事情时也存在——尽管这本身最近已经通过 [AWS 对 VPC 网络](https://aws.amazon.com/blogs/compute/announcing-improved-vpc-networking-for-aws-lambda-functions/)的更改而得到了改善。

![](img/360574a97dca5cd93f02f86ed2f6647a.png)

想象一只笨重的大乌龟，被冰覆盖着，从冬眠中醒来，仍然昏昏沉沉( [brumation](https://www.merriam-webster.com/dictionary/brumation) )，你非常接近使用 Java 的 AWS Lambda 冷启动的感觉。这很大程度上归结于我们使用 Java 编写 lambda 函数的方式。有些事情我们已经依赖或认为是理所当然的——比如具有看似无限的 CPU 和内存的基础设施、廉价的反射以及服务生命周期内的一次启动——在 lambda 环境中并不成立。如果我们在我们正在处理的系统的约束下工作，并在考虑这些约束的情况下做出一些改进，我们就可以从冰上的海龟变成旱冰鞋上的海龟，并告别缓慢的冷启动。

# 有什么解决办法？尝试这些 AWS Lambda Java 最佳实践

我们很幸运，因为我们可以做很多事情来提高 Java 函数的性能。在本教程中，我将介绍 11 个 AWS Lambda Java 最佳实践，并尝试给出一些实际例子来说明这是如何工作的。

## 衡量当前绩效

![](img/744e6b2a9b1dd0390d45332109eb8bf2.png)

你要做的第一件事是评估你当前的表现。不幸的是，这并不像一只手拿着卡钳，一只手拿着海龟那么简单。相反，您可以通过将您的函数部署到 AWS，调用它，然后测量完成调用需要多长时间来完成。就像前面提到的卡尺一样，有一些工具可以用来测量 lambda 的性能。一个这样的工具是 [Gatling](https://gatling.io/) ，你可以用它来调用你的 lambda 很多次，然后看看报告中的响应时间，它会产生一个很好的可视化效果，并给你原始的数字。

![](img/6ef7a2e6673404c838e47d416f08dff3.png)

Report I generated in Gatling

还有一个叫做 [X 射线](https://aws.amazon.com/xray/)的 AWS 仪器工具非常出色。基本思想是用 X 射线调用来检测代码，然后在 AWS 控制台中检查结果，看事情要花多长时间。有许多内置的记录器可以监视 HTTP 调用、AWS SDK 调用等。，该工具可以在检测 lambda 函数时自动显示 JVM 初始化时间。

除此之外，还有第三方提供类似的功能，如 [AppDynamics](https://www.appdynamics.com/) 和 [DataDog](https://www.datadoghq.com/) 值得关注。

## 分配更多内存

![](img/a171b6e4d9f313eeb737b62eff66b16d.png)

大象可能永远不会忘记，但一只 200 岁的乌龟肯定看到了一些东西。如果你给 lambda 函数分配更多的内存，它会运行得更快。好了，博文结束了，我现在可以不打字了吗？

好吧，所以这个有点逃避。但是如果你发现你的 128mb lambda 函数启动和执行的时间太长，你可以把它换成 3008mb 的 lambda 函数，它会运行得更快。这部分是由于 AWS Lambda 中的线性扩展资源模型，因为 [Lambda 与配置的内存量](https://docs.aws.amazon.com/lambda/latest/dg/configuration-console.html)成比例地线性分配 CPU 功率。这意味着更多的内存意味着更多的 CPU，这意味着更快的功能。

不幸的是，这也意味着更多的钱。将所有 128mb 的 lambda 函数扩展到 3008mb 的不利之处在于，运行的[成本可能是运行](https://aws.amazon.com/lambda/pricing/)的 25 倍左右(取决于请求的数量和长度)。这听起来可能很糟糕，但它可能不像看起来那么重要，因为这完全取决于你的流量概况。在我的团队中，我们有一项服务，每月约有 30 万个呼叫，其中 99%的呼叫在 100 毫秒内完成。在这种情况下，从 128mb 迁移到 3008mb 将使我们每月的计算费用从大约 0.06 美元增加到大约 1.47 美元。相对来说是一个很大的增长，但从表面上看，实际上并没有什么大不了的。

然而，如果你的流量档案更接近于我们的姐妹团队所拥有的服务，你可能会看到一个更大的账单。他们的流量概况接近每月 400，000，000 个请求，耗时约 300 毫秒——这使你从每月 2500 美元(128mb 内存)到惊人的 58750 美元。此外，lambda 上可能有数百个服务，其流量配置文件介于这两个极端之间，因此在这种情况下，保持较低的内存大小绝对符合您的最佳利益。

## 减少依赖性

![](img/86f827c35702e3e3fcb1a02faf3c7fde.png)

减少部署的 jar 中依赖项的数量将对函数的冷启动时间产生积极的影响。最初我认为这与 jar 的大小有关——更多的依赖项意味着将这些依赖项打包到 jar 中，这意味着更大的 jar 文件。理论上，这需要更长的时间来拉低，因此速度较慢。事实证明，虽然这是真的，但它在大格局中的影响可以忽略不计。

不管冷启动与否，jar 几乎是直接被缓存的，网络 IO 永远不会成为问题，除非你有一个大得离谱的文件。拥有大量依赖项的实际问题是您必须加载的类的数量。

让我们来看一个示例[spring oot](https://spring.io/projects/spring-boot)应用程序，它利用[spring oot 入门指南](https://spring.io/guides/gs/spring-boot/)返回“Hello World”。

这个项目在启动时加载了大约 5，900 个类，在第一次调用时又加载了大约 200 个。如果你像我一样，你从来没有真正研究过这个问题——在我的笔记本电脑上，启动服务器接受 HTTP 调用需要大约 1.59 秒，而 [jstat](https://docs.oracle.com/javase/7/docs/technotes/tools/share/jstat.html) 报告所有的类都在大约 1.8 秒内完成加载。如果您运行在具有大量 CPU 和内存的大型 EC2 服务器上，这可能会更快。

我们忽略了这样一个事实，即大约 6000 个服务于一个 HTTP 请求的类是一个非常大的数目。从长远来看——如果运行“Hello World”jar，加载的基本 Java 运行时大约有 460 个类。使用 *aws-lambda-java-core* 依赖项运行 lambda 的最低要求是增加另外 9 个类，所以对于一个全功能的 lambda 函数，我们只需要不到 500 个类。当使用 [API 网关](https://aws.amazon.com/api-gateway/)或[应用负载平衡器](https://docs.aws.amazon.com/elasticloadbalancing/latest/application/introduction.html)连接时，您仍然能够服务 HTTP 请求。

如前所述，您可以使用 *jstat* 自己做这个实验，它会给出加载的类的数量和花费的时间:

```
$ java -jar <your-jar>
$ jstat -class <pid> 1000
```

或者，您可以使用一个更成熟的工具，如 [jVisualVM](https://docs.oracle.com/javase/8/docs/technotes/tools/unix/jvisualvm.html) 并执行堆转储，这将为您提供加载的类的概要以及当前正在使用的类的实例数量:

```
$ jvisualvm — openpid <pid>
```

您甚至可以在启动 jar 时使用一些详细的类日志记录，这将为您提供已经加载的所有单个类的列表。这种方法对于找出哪些依赖项花费了您最多的类非常有用:

```
$ java -cp your.jar -verbose:class MainClass | grep Loaded > loaded.txt
```

从这个文件中，您可以执行一些简单的 grep 命令来找出哪些依赖项的开销最大。

举一个实际的例子，让我们看看前面的简单的 SpringBoot 应用程序。我将把命令和输出一行一行地分开，而不是使用神奇的一行程序，这样更容易理解。

```
$ java -cp build/libs/springboot-app.jar -verbose:class org.springframework.boot.loader.JarLauncher > startup.txt 
$ cat startup.txt | grep Loaded | sort > loaded-classes.txt
```

这将为您提供一个文本文件，其中包含您在启动服务时加载的大约 6000 行类，我们可以检查该文件以获取一些数据。例如，这些类中有多少是直接依赖 Spring 的？

```
$ cat loaded-classes.txt | grep org.springframework | wc -l 2496
```

哎哟！那可是很多课啊。

现在，我并不主张在任何地方完全重新发明轮子，并且永远不再使用另一个依赖项，但是花时间适当地检查您正在引入的依赖项，并执行成本效益分析，以确定是否值得增加成本或增加延迟，这是我们都应该做的事情。

*有趣的海龟事实:海龟平均一窝产 110 个蛋，一个季节平均产 2 到 8 窝。他们不养育他们的幼仔，谈论减少依赖！*

## 使用 AWS SDK v2

![](img/fc2cc5885c62873ea045b4c99bad8c83.png)

你知道 2018 年底发布了新版本的 [AWS SDK](https://aws.amazon.com/sdk-for-java/) 吗？我直到最近才使用它，并且惊讶地发现它在这种情况下是如何有用的。SDK 的 V1 于 2010 年 3 月首次提供使用，尽管直到今天它仍在继续接收更新，但 API 和底层基础架构仍然与十年前的构建方式非常相似。V2 最近于 2018 年 11 月面向大众发布，并针对 lambda 等无服务器框架进行了更好的优化。使用 V2 而不是 V1 的一些好处是，它包含更少的依赖性，允许非阻塞 IO，并且比原始库有更好的配置选项，包括利用 HTTP 插件定制 HTTP 库的能力，等等。

不要忘记排除你不使用的可传递依赖——V2 包括 Netty 和 Apache HTTP 库，在大多数用例中你通常不需要它们。

还在用 V1 吗？亚马逊提供了一个[迁移指南](https://docs.aws.amazon.com/sdk-for-java/v2/migration-guide/what-is-java-migration.html)，用于更新你的代码以使用库的 V2 而不是 V1。

## 使用基本的 HTTP 客户端

![](img/a62b26fc52d58a37bc03059dcb14b790.png)

AWS SDK 附带了许多不同的 HTTP 客户端库，可用于进行 SDK 调用。这些都是会唱歌、会跳舞的库，它们可以进行连接池和一大堆其他的东西，这些东西在一些场景中非常有用，比如当你在一个长生命周期的服务器上时。

Lambda 不属于这种情况。实际上，大多数时候您将进行一次或者几次 HTTP 调用，然后将结果返回给您的客户机。由于 lambda 函数一次只能处理一个传入的请求，所以您甚至不太可能有机会执行您创建的任何其他连接。这意味着任何并发请求都将进入一个单独的 lambda 函数(有自己的连接池)。要么这样，要么你已经完成了在传递回你的响应时已经在使用的连接，所以它仍然可以被这个 lambda 的下一个请求再次使用。

内置的 Java HTTP 客户端对于 lambda 函数中的几乎所有用例来说都应该足够好，所以尽可能地使用它，除非您有令人信服的理由使用更复杂的东西。

## 完全指定 AWS SDK 客户端

![](img/6753d4dcf3a11acaf7a5bef91ca29549.png)

好的，你已经从 AWS SDK 的 V1 搬到了 V2，但是你还可以做更多的改进。其中之一是完全指定单个 SDK 客户端的配置，而不是使用作为提供者链一部分的自动发现。

您已经控制了环境和 lambda 函数，所以您应该知道您的凭证来自哪里、您正在运行的区域、您正在使用的 AWS 服务的服务端点等等。通过预先指定这些，您可以确保 SDK 不需要做比初始化时更多的工作。一个很好的例子是，如果你没有指定一个端点覆盖，那么 SDK 会使用 [Jackson](https://github.com/FasterXML/jackson) 读取并解析一个包含所有地区所有服务的所有端点的大型 JSON 文件——所以这里有 IO 和反射来解决一些已知的问题。

## 移除昂贵的依赖注入框架

![](img/ad5770d334f21879396d05036cb84f08.png)

虽然注射可能对海龟有好处，但在 lambda 环境中对 Java 来说就不那么健康了。许多最常用的依赖注入框架运行起来可能很昂贵。当你要求它们时，它们会使用类路径扫描和反射来创建一大堆对象，并将它们链接在一起。这在 lambda 环境中不太好，因为在内存和 CPU 有限的环境中，反射非常慢。

如果你正在使用基于反射的依赖注入框架，你有两个选择来提高速度:

1.  **转向一个不是基于反射的框架**——比如[匕首](https://github.com/google/dagger)。这使用注释在编译时预先生成 Java 源代码，因此您不必在以后进行任何反射或字节码生成——这样会快很多。
2.  **完全移除依赖注入框架。这听起来可能有些极端，但请听我说。如果你的 lambda 函数足够小(它应该足够小)，那么拥有一个完整的依赖注入框架所带来的好处并不像你想象的那么明显。一个三层的 SpringBoot 应用程序有一大堆深入到应用程序中的依赖关系(多个层),让依赖注入框架为您处理一切肯定会有好处。如果我们正在编写没有几层的小型 lambda 函数，那么依赖注入框架就没什么用了。一个有用的 lambda 可以有一个和 Handler>Service>dynamo client 一样小的层次结构，这更容易管理。这消除了对阿迪框架的需求，使得自己初始化对象并将它们传递给构造函数成为处理依赖链的一种更简单的方式。**

## 消除反射

![](img/747309967aa49d0586627daabf8a19fc.png)

镜子，镜子，在湖上，我还能做些什么改变？摆脱反思，或者至少尽你最大的努力减少它。如上所述，在 lambda 等内存受限的环境中，反射真的很慢。这意味着你应该尽可能地避免自己动手，甚至可以毫不犹豫地开始使用不同的库，而不是我们经常使用的库。从前面的观点来看，依赖注入是一个明显的问题，我们已经讨论了一些选项。还有其他领域，比如使用 Jackson 的 JSON 编组和解组，有时可以用同样的方式替换代码生成库，比如 [Moshi](https://github.com/square/moshi) 。

这说起来容易做起来难，因为 AWS SDK 本身使用 Jackson 来执行自己的解组。然而，您也许可以在自己的代码中使用一些不同的东西。在我们的一些服务中，我们使用 Moshi 对 ALB 请求和响应进行编组和解组，利用 RequestStreamHandler 并对输入和输出流进行操作，这比 Jackson 编组更快，体验很好。

## **在初始化时初始化依赖关系**

![](img/96f367737115646f704bd87648c2a365.png)

你知道龟兔赛跑的故事吗？尽管兔子开始跑得很快，但他却变得自满，最终输掉了比赛。嗯，Lambda 函数完全不是这样的-你开始得越快，完成得越快，有一个小技巧可以让你的乌龟像兔子一样开始比赛。

先说理论。Lambda 函数在被调用时会经历两个阶段- *初始化*和*运行时*。初始化只在 lambda 函数在没有执行上下文的情况下启动时运行，这就是我们所说的“冷启动”之后，它将尝试重用执行上下文，因此只运行运行时阶段。初始化阶段负责使您的函数代码可调用的一切——JVM 启动、对象初始化等。-在运行时阶段调用 handle 方法所需的一切。

这种两阶段方法的一个不太为人所知的事实是，在初始化期间，您可以提高对 CPU 的访问，然后在运行时降低访问。这意味着任何昂贵的操作最好在初始化时完成，因为它们将在访问更多 CPU 时更快地完成。

在现实世界中，这对于大多数实现来说可能不是问题——作为对象实例化的一部分创建的任何东西都会在初始化时发生。这包括静态字段和块、实例字段和块以及构造函数调用。大多数情况下，我们会在初始化时创建依赖项，对吗？但是，我们在第一次调用时延迟创建延迟加载的异议并不罕见，因为它很昂贵，并且您无意中让它在我们受到 CPU 限制时被创建——这可能会比我们只是提前创建它花费更长的时间。

## **初始化时的主要依赖关系**

![](img/632f25bca831e87050a28dc56683d886.png)

这一步可能会比上一步更有用。在我们初始化了我们的 SDK 客户端并在初始化阶段设置好它们之后，这应该意味着我们可以在运行阶段毫无问题地使用它们，对吗？

没有那么多。很多 AWS SDKs 都是延迟加载的——所以即使你预先初始化 DynamoDB 客户端，很多*实际的*初始化也不会发生，直到你调用 GetItem 或 PutItem。例如，DynamoDB 中的 PutItem 可能需要**9**秒来初始化 Jackson marshallers、启动连接等。第一次调用它时，内存占用很小。

您可以通过使用上面的技巧来绕过这种昂贵的运行时初始化，但这次我们将进行“启动”方法调用，而不仅仅是实例化对象。这感觉不太好，但是如果将 DynamoDB PutItem 调用移到初始化阶段，访问提升 CPU 只需 700 毫秒，然后运行时阶段的后续调用将有一个准备就绪的客户端。

在您的“healthcheck”调用中使用这种方法可能是值得的——使用 DynamoDB 客户端对一个已知项目进行 GetItem 调用，这不仅会为您的客户端做好准备，还会检查您与 DynamoDB 的连接，使其成为更有用的健康检查。

## 通过 **GraalVM** 使用本地可执行文件

![](img/05e1f93f13083d30eeae2049b70efb5a.png)

这一部分肯定值得比这篇博客文章更深入的展开，但是未来开发的一个重要收获是使用诸如 [GraalVM](https://www.graalvm.org/) 与 [Micronaut](https://micronaut.io/) 或 [Quarkus](https://quarkus.io/) 结合的工具来生成本机可执行文件，这些文件作为本机可执行文件直接在底层操作系统上运行，而不是在 JVM 上执行。它的设置看起来相当复杂，并且有很多信息，我无法在这里一一介绍，但它可能是一些有趣的东西，值得进一步研究。这种方法还不完全成熟，所以在生产服务中使用它之前，我会小心谨慎——但是这方面的事情发展很快，这肯定是需要注意的。

***资源了解更多:***

1.  上面各个产品的链接包含入门指南，应该可以让您相对快速地入门。
2.  这篇[博客文章](/journeyintocloud/hello-world-on-quarkus-graalvm-gradle-5209357b423a)深入到使用 Quarkus 创建一个 HelloWorld 应用程序，它可以让您在几分钟内启动并运行。
3.  Opsgenie 上的这篇[文章通过一个使用 Golang 运行时在 AWS Lambda 中运行的本地 Java 示例，深入探讨了 GraalVM 的优势。](https://engineering.opsgenie.com/run-native-java-using-graalvm-in-aws-lambda-with-golang-ba86e27930bf)

# 结论

使用 Java 时，我们可以遵循许多最佳实践来减少 AWS Lambda 冷启动时间。我们已经讨论了一些领域，比如测量您当前的性能，减少您的依赖项的数量和复杂性，以及减少反射的重要性。此外，我们还简要介绍了一些东西，如 Quarkus 和 GraalVM，它们保证了将来更深入的研究。

我希望你喜欢这篇 AWS Lambda Java 教程。要了解更多关于我们已经讨论过的内容，我推荐观看关于 AWS Lambda 和 Java 最佳实践的[AWS re:Invent 专题讲座](https://www.youtube.com/watch?v=ddg1u5HLwg8)。很漂亮-你应该去看看。但可悲的是，没有任何海龟参考。

*原载于*[](https://www.capitalone.com/tech/cloud/aws-lambda-java-tutorial-reduce-cold-starts/)**。**

**披露声明:2020 资本一。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。**