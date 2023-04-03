# Android Gradle 插件中的新 API

> 原文：<https://medium.com/androiddevelopers/new-apis-in-the-android-gradle-plugin-f5325742e614?source=collection_archive---------0----------------------->

![](img/4f2da04570761ba8f65cac83f1062297.png)

*与* [合著*杰罗姆·多切兹*](https://medium.com/u/c7609de1c53?source=post_page-----f5325742e614--------------------------------)

Android Gradle 插件是 Android 应用程序支持的构建系统，支持编译许多不同类型的源代码，并将它们链接到一个可以在物理 Android 设备或仿真器上运行的应用程序中。

Android Gradle 插件与 Android Studio 分离——Android Studio 可以打开使用相应 Android Gradle 插件或任何以前稳定版本的项目，目前一直到 Android Gradle 插件 1.1。

Gradle 和 Android Gradle 插件提供了根据用例以不同方式配置和扩展构建的能力。

对于配置主要是声明性的简单项目，Android Gradle 插件可以在用 [Gradle 构建语言](https://docs.gradle.org/current/userguide/groovy_build_script_primer.html)编写的`build.gradle`文件中进行配置，或者在 Groovy 脚本之上构建的领域特定语言(DSL ),或者用最近引入的 [Gradle Kotlin DSL](https://docs.gradle.org/current/userguide/kotlin_dsl.html) 编写的`build.gradle.kts`文件。

Android Gradle Plugin DSL 是在 Gradle Kotlin DSL 推出之前编写的，需要更新才能与 Gradle Kotlin DSL 配合使用。Android Gradle 插件 4.1 现在完全支持新的 Kotlin DSL。

DSL 并不是扩展构建的唯一方式。为了在项目中重用，可以用 Groovy 或 Kotlin 编写脚本，并在多个构建文件中应用，但通常定制命令式逻辑的更好地方是 [buildSrc](https://docs.gradle.org/current/userguide/organizing_gradle_projects.html#sec:build_sources) ，它可以使用 Java、Groovy 和 Kotlin，并且为了在单独的项目中重用，或者可以通过用 Java、Groovy 或 Kotlin 编写的[二进制插件](https://docs.gradle.org/current/userguide/custom_plugins.html#sec:custom_plugins_standalone_project)定制构建。Android Gradle 插件本身就是一个二进制插件。

许多构建作者希望扩展 Android Gradle 插件，例如通过以细粒度的方式配置特定的变体，生成有助于最终工件的附加工件或转换中间工件。Android Gradle 插件已经为所有这些用例提供了 API，但它们需要更新以兼容 [Gradle 的惰性配置](https://docs.gradle.org/current/userguide/lazy_configuration.html)，并减少与 Android Gradle 插件内部实现细节的耦合，从而为未来的更大灵活性提供更好的基础。

# Android Gradle 插件中的新特性:DSL 接口和文档

*   Android Gradle 插件 4.1 包括从现有实现中提取的新 DSL 接口。
*   从这些接口中，[发布了 Android Gradle 插件的 DSL 和 API 的新文档](https://developer.android.com/reference/tools/gradle-api)。
*   为了更好地支持 Kotlin 脚本，现在 Kotlin 中提供了 API 和 DSL 接口，这导致我们对现有 Kotlin 脚本用户进行了一些突破性的更改。

随着 Android Gradle 插件周围的生态系统的增长，我们现有的 DSL 定义已经跟不上了。对于哪些 API 是稳定的，哪些是实验性的，没有一个明确的定义，更糟糕的是，一些内部实现细节泄露到了 DSL 和插件实现类的表面 API 中。此外，我们的文档生成器是 Gradle 的旧版本，缺少对 Java 8 语言特性和 Kotlin 的支持。

由于构建作者与 Android Gradle 项目的 API 交互的各种方式的不同兼容性要求，清理这一切变得更具挑战性。简单的项目可能只使用 Groovy DSL，有些项目已经采用了 Kotlin DSL，有些项目使用 buildSrc，同时使用 Java 和 Kotlin，还有许多项目引入了二进制插件。在清理过程中，我们希望避免破坏与任何用例的兼容性，特别是 Groovy DSL 兼容性和插件的二进制兼容性。

为了保持二进制兼容性，我们必须保留现有的 DSL 实现类。我们决定在 Kotlin 的`gradle-api`工件中创建接口，这些接口定义了 DSL 的所有公共函数，并由现有的实现类实现，目标是成为 Android Gradle 插件 API 的唯一真实来源。Android Gradle 插件的[文档](https://developer.android.com/reference/tools/gradle-api)现在直接从这些 Kotlin 接口生成，提供了一组文档，对每个使用或扩展 Android Gradle 插件的人都有帮助，无论是编写 Groovy 或 Kotlin 脚本，构建 Src 还是添加功能的二进制插件。

在 Kotlin 中定义接口时，我们强制明确了几个关于空性和可变性的决定。从长远来看，这是非常有帮助的，但不幸的是，对于构建作者已经接受 Gradle 的 Kotlin 脚本支持的现有项目来说，这可能是一个源代码级别的突破性更改，虽然大多数突破会导致某些错误的源代码兼容性突破，这些错误在以前的版本中会表现为运行时错误，但在某些情况下，我们不得不决定对以前可以工作的东西进行突破性更改。我们希望通过现在进行这些改变，我们可以避免在未来做出这种突破性的改变。

我们被迫做出的最具挑战性的选择是如何表达在 DSL 中被设计为变异的集合类型。这些类型现在统一定义为`val collection: MutableCollectionType`。

这意味着在 Kotlin 脚本中不能再为以前支持的收藏分配收藏，`collection = collectionTypeOf(...)`

然而，改变集合是统一支持的，所以`collection += …`和`collection.add(...)` 现在应该可以在任何地方工作。

虽然 Gradle Kotlin DSL 和 Gradle Groovy DSL 看起来非常相似，但是有些在 Groovy 中符合人体工程学的构造在 Kotlin 脚本中并不适用。一个常见的例子就是使用`=`操作符。大多数 Android 项目使用整数 Android `compileSdkVersion`，在 Groovy 脚本中，编写`compileSdkVersion = 30`会导致调用`setCompileSdkVersion(30)`。然而，对于一些带有预览版和插件的用例，编译 sdk 是一个字符串，例如`compileSdkVersion = "android-R"`。在 Groovy 中，这只是映射到一个不同的`setCompileSdkVersion(...)`方法，而在 Kotlin 中，我们被迫为属性分配一个类型。

在 Android Gradle 插件 4.1 中，我们引入了一些新的属性，使得用 Kotlin 脚本编写更加容易。我们期望大多数构建简单地使用`var compileSdk: Int`，对于更特殊的情况，我们引入了`var compileSdkPreview: String`和助手方法`compileSdkAddon(vendor: [String](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-string/index.html), name: [String](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-string/index.html), version: [Int](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-int/index.html)): [Unit](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin/-unit/index.html)`。我们的目标是让这些新属性最终取代`compileSdkVersion`，但是在社区迁移之前，它们将继续并肩工作。

# 新变体 API

> 更新:本节已经更新，以反映自最初发布以来 API 中命名的变化。

Android Gradle 插件已经有了一个变体 API，但是它的设计选择有一些限制。我们决定引入一个新的 API 来解决这些问题，提供一个更清晰的扩展模型，与 Android Gradle 插件的内部分离，而不是通过向现有 API 添加新的扩展性来继续积累技术债务。现有的 API 将暂时保留，我们将努力帮助插件和作者迁移到新系统。我们还发布了一个使用新变体和工件 API 的样本库[,我们将继续对其进行扩展。](https://github.com/android/gradle-recipes)

与以前的 API 相比，新的变体 API 在配置期间运行得更早。这允许以影响构建流程的方式修改变体，不像以前的 API，在以前的 API 中，这些决定在 API 运行时就已经做出了。这些改变既可以是显式的，允许设置影响构建流程的属性，也可以是隐式的，如果存在与变体 API 中公开的内容不兼容的优化，则可以简单地在使用该 API 的条件下完成。

新的 API 进一步分成不同的回调，`beforeVariants`和`onVariants`，并分别用于测试组件`beforeAndroidTest`和`onAndroidTest`，以及`beforeUnitTest`和`onUnitTest`。

`beforeVariants`为将要创建的每个对象运行给定的操作。构建作者和插件作者可以使用它来定制影响整个构建流程的属性，[，例如是否启用了特定的变体或测试组件](https://github.com/android/gradle-recipes/blob/agp-4.2/BuildSrc/testVariantFilteringOnBuildType/buildSrc/src/main/kotlin/CustomPlugin.kt)。之后，调用`onVariants`回调，允许构建作者和插件作者创建定制逻辑并注册消耗或修改构建中间产品的任务。

以下是 Android 项目构建所经历的各个阶段的概述:

1.  运行构建脚本，允许构建和插件配置 Android Gradle 插件 DSL 对象以及注册变体回调。
2.  Android Gradle 插件结合了构建类型和产品风格来创建变体和测试组件。
3.  为每个变量调用`beforeVariants` API，允许变量设置的定制，为每个测试组件调用`before[Android|Unit]Test` API。
4.  为每个被启用的变体调用`onVariants` API，为每个被启用的测试组件调用`on[Android|Unit]Test` API，从而允许使用或修改构建中间产品的任务的注册。
5.  Android Gradle 插件在调用了`before[Component]`和`on[Component]`回调之后为变量注册任务。
6.  使用注册的任务为每个变体调用先前的变体 API。
7.  Gradle 计算任务图，构建可以开始执行。

`onVariants` API 利用了[的梯度属性和提供者](https://docs.gradle.org/current/userguide/lazy_configuration.html)。属性允许延迟计算值，并在同一位置跟踪依赖信息，允许使用 API，而不必担心任何特定属性的值何时已知。`beforeVariants` API 不使用属性，因为值是在配置时使用的，所以不会因为懒惰而受益。它确实受益于在配置期间更早地运行，这就是为什么它可以提供比以前的 API 更大的灵活性，以前的 API 受任务创建后运行的限制。

## 工件 API

Android Gradle 插件的先前变体 API 暴露了构建的任务，以便允许构建的消费或扩展。例如，在 Android Gradle Plugin 4.0 中，为了将库的 AAR 输出应用到自定义任务，构建作者或插件作者可能已经编写了如下内容:

虽然这看起来很简单，特别是在这个小例子中，输出已经作为附带依赖信息的提供者可用，但这与产生最终 AAR 的任务的实现是紧密耦合的。如果代码被编译成二进制插件，它将阻止 Android Gradle 插件从任务中移除公共方法，改变任务的类型或将其拆分以改善构建流程。例如，我们考虑过从使用内置的 Gradle Zip 任务切换到构建 AARs 的自定义任务，但是我们还没有这样做，因为它破坏了常用的二进制插件。

上面的例子只获得定制任务消费的工件。如果可能的话，使用以前的 API 来替换或转换工件通常是非常脆弱的，因为它需要协调多个任务并更新工件的每个消费者。

使用 Android Gradle Plugin 4.2 中的新构件 API，您现在可以编写:

这里的不同之处在于，对任务的引用消失了:API 提供了更多的灵活性，同时也与 Android Gradle 插件中的内部实现解耦。

Android Gradle 插件为所有输入、输出和中间文件管理附加到每个变体的工件的注册表，表示为附加了依赖信息的提供者。这个注册表在整个 Android Gradle 插件中使用，以取代任务的手动连接，并管理工件的位置，以避免意外冲突和不一致。

这些工件的子集通过新的变体属性 API 公开。

这个注册表的键是`[Artifact](https://developer.android.com/reference/tools/gradle-api/com/android/build/api/artifact/Artifact)`的实例。无论工件是文件还是目录，都被表示为其类型的一部分。编写自定义任务时，使用正确的对应`[Property](https://docs.gradle.org/current/javadoc/org/gradle/api/provider/Property.html)`，即`[DirectoryProperty](https://docs.gradle.org/current/javadoc/org/gradle/api/file/DirectoryProperty.html)`或`[RegularFileProperty](https://docs.gradle.org/current/javadoc/org/gradle/api/file/RegularFileProperty.html)`，这一点很重要。工件的基数也编码在它的类型中，当处理一个`[MultipleArtifact](https://developer.android.com/reference/tools/gradle-api/com/android/build/api/artifact/Artifact.MultipleArtifact)`类型时，必须使用特定的 API 与`[ListProperty](https://docs.gradle.org/current/javadoc/org/gradle/api/provider/ListProperty.html)`交互。

修饰工件的其他接口指出了可以在工件上执行的操作类型。这种操作允许定制任务通过用[替换](https://developer.android.com/reference/tools/gradle-api/com/android/build/api/artifact/Artifact.Replaceable)、[转换](https://developer.android.com/reference/tools/gradle-api/com/android/build/api/artifact/Artifact.Transformable)或[添加](https://developer.android.com/reference/tools/gradle-api/com/android/build/api/artifact/Artifact.Appendable)工件来参与 Android 构建过程，所有这些都不需要具体了解参与构建过程的其他任务是如何生产或消费这些项目的。

现在可以对工件进行的操作包括:

## 得到

该操作获得工件的最终版本。不管工件是如何产生的，有时是如何被任务转换的，调用`[get](https://developer.android.com/reference/tools/gradle-api/com/android/build/api/artifact/Artifacts.html#get(com.android.build.api.artifact.ArtifactType))`(或者`[getAll](https://developer.android.com/reference/tools/gradle-api/com/android/build/api/artifact/Artifacts.html#getall)`代表`[MultipleArtifact](https://developer.android.com/reference/tools/gradle-api/com/android/build/api/artifact/Artifact.MultipleArtifact)` s)将总是确保工件的最终版本被提供。它是一个只读值:不允许修改由`get`获得的工件。因此，`get`方法的返回类型是`Provider`而不是`Property`。这个`Provider`只能作为任务输入。

例如，您可能对访问合并后的清单感兴趣，不是为了修改它，而是为了确保权限没有改变:

现在您可以连接您的任务:

## 转换

这个操作以某种方式修改了工件。原始工件作为给定任务的输入，以及输出转换后工件的新位置。

让我们看一个通过将版本设置为 git head value 来转换合并清单的任务。

首先，让我们定义将计算 git 头的任务:

现在，创建一个任务，它将使用前一个任务计算的 git 版本来转换清单文件。

布线清楚地表明了意图:

## 附加

Append 只与用 MultipleArtifact 修饰的工件类型相关。因为这种类型被表示为目录或正则文件的列表，所以任务可以声明将被附加到列表的输出。

接线与其他操作类似:

注意，在这一点上，我们还没有发布一个公共的`[MultipleArtifact](https://developer.android.com/reference/tools/gradle-api/com/android/build/api/artifact/Artifact.MultipleArtifact)`工件，所以这个 API 只会在将来变得有用。

## 创造

这个操作用一个新的提供者替换一个工件的当前提供者，放弃所有以前的生产者。这是一个“out”操作，任务声明自己是工件的唯一提供者。如果有多个任务声明自己是工件提供者，那么最后一个任务获胜。

例如，自定义任务可能不使用内置的清单合并，而是依赖于签入源代码管理工具的手动合并的清单。

连线至:

当一个定制任务正在创建一个工件时，该工件的所有原始生产者/转换者都不会成为构建过程的一部分，除非需要产生另一个工件。我们不期望很多构建使用这一点，因为我们考虑的大多数用例将通过转换得到更好的服务。如果你发现自己正在使用 creation 解决 Android Gradle 插件中的一个限制，请[提交一个问题或功能请求](https://developer.android.com/studio/report-bugs#build-bugs)。

通过关注工件而不是任务，定制插件或构建脚本可以安全地扩展 Android Gradle 插件，而不会受到构建流程变化或任务实现等私有细节的影响，我们希望这些变化意味着构建作者可以在未来更容易地升级他们的 Gradle 插件。

我们计划在即将到来的版本中允许更多的变体配置，并使更多的工件作为公共类型可用，我们将继续添加更多的[示例](https://github.com/android/gradle-recipes/)。