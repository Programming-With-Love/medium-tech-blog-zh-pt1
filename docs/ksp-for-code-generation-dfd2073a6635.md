# 代码生成的 KSP

> 原文：<https://medium.com/google-developer-experts/ksp-for-code-generation-dfd2073a6635?source=collection_archive---------0----------------------->

![](img/fb5e4284b362c2026fbdfe9c01e0cb6a.png)

Photo by [Birmingham Museums Trust](https://unsplash.com/@birminghammuseumstrust?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/assembly-line?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

作为一名开发人员，我们每天所做的就是编写代码。不仅仅是无意识地写，我们都希望我们能写出没有缺陷的代码。为此，有很多工具可以帮助我们提高质量，比如单元测试和静态分析。但是，没有人能保证他们的代码没有任何错误。

人为错误似乎是不可避免的，这就是为什么我们可能希望利用代码生成工具来帮助我们编写更少的代码。这不仅会减少我们花费的时间，还会提高代码质量。如果您熟悉 Java，您可能以前听说过注释处理，如果您是 Kotlin 开发人员，现在您已经有了 KSP。

# KSP

KSP 代表科特林符号处理。顾名思义，它不仅仅是一个代码生成工具，还是一个编译器插件 API，可以帮助你在编译时获得完整的源代码信息。你可以做任何事情，比如生成更多的代码，分析结构，甚至抛出一个额外的错误来中断构建，代码生成无疑是一个非常强大的用例，我们今天将主要关注这个主题。

## 代码生成

通常，我们将创建一个单独的处理器模块，其中包含一些在编译时会被触发的处理器。每个处理器可以读取主应用程序源代码信息以生成代码，生成的代码将与您的源代码一起编译以形成最终输出。

使用预定义的注释是一种常见的技巧。处理器模块和您的应用程序模块都依赖于同一个注释模块，处理器模块可以很容易地通过这些注释过滤来自应用程序模块的信息，但这完全取决于您的偏好。

在下一节中，我将结合详细的分步介绍和一个简单的用例来展示 KSP 可以为我们做些什么。

# 例子

比方说，我们喜欢构建一些工厂模式，如下所示:

```
public enum class AnimalType {
    CAT,
    DOG,
}

public fun AnimalFactory(key: AnimalType): Animal = when (key) {
    AnimalType.CAT -> Cat()
    AnimalType.DOG -> Dog()
}interface Animalclass Dog : Animalclass Cat : Animal
```

当我们调用`AnimalFactory(AnimalType.CAT)`时，我们希望得到一个`Cat`实例——而不是担心如何创建它。这种模式很漂亮，但是不可扩展，正如你所看到的，每当我们想要添加一个新的类型，我们需要一次又一次地修改同一个函数。我们能做些什么来使它变得更好？

最重要的部分是定义那些动物类和接口，其余的似乎只是遵循一些规则。让我们致力于通过 KSP 自动构建`AnimalType`和`AnimalFactory`，这样我们就不用再写样板代码了。

## 更新项目级配置

首先，更新你的根目录`build.gradle`来启用 KSP 特性，如下所示:

```
plugins {
    id 'org.jetbrains.kotlin.jvm' version '1.7.10' apply false 
    id 'com.google.devtools.ksp' version '1.7.10-1.0.6' apply false
} buildscript {
    dependencies {
        classpath 'org.jetbrains.kotlin:kotlin-gradle-plugin:1.7.10'     
    } 
}
```

> 这里可以找到 KSP 版本:[https://github.com/google/ksp/releases](https://github.com/google/ksp/releases)

## 注释模块

我们将首先在一个新模块中为应用程序和处理器模块定义注释，以便在它们之间传递元数据。

```
@Target(AnnotationTarget.CLASS)
@Retention(AnnotationRetention.SOURCE)
annotation class AutoElement@Target(AnnotationTarget.CLASS)
@Retention(AnnotationRetention.SOURCE)
annotation class AutoFactory
```

我们假设`AutoElement`将添加到具体类中，而`AutoFactory`用于声明返回类型。稍后，我们将依靠这个契约来过滤构建图表所需的信息。

> 这里您可以看到注释类本身也有两个合格的注释。`@Target`用来限制它可以使用的地方。假设我们只想在类或接口上标记它，我们选择`CLASS`。`@Retention`用于声明注释的生命周期，对于 KSP 用例，我们在编译后不再需要它，所以我们将其设置为`SOURCE`。

## 处理器组件

这是所有奇迹发生的地方！我们将很快创建处理器来自动生成代码。在此之前，我们需要像下面这样声明 KSP 依赖关系`build.gradle`:

```
plugins {
    id 'org.jetbrains.kotlin.jvm'
}

dependencies {
    implementation project(":annotation")
    implementation 'com.google.devtools.ksp:symbol-processing-api:1.7.10-1.0.6'
}
```

一个处理器模块可能有几个处理器，这里是所有处理器都应该实现的基本`SymbolProcessor`接口。

```
interface SymbolProcessor {
     fun process(resolver: Resolver): List<KSAnnotated> //main logic
     fun finish() {}
     fun onError() {} 
}
```

process 函数是解析代码的入口点，而`Resolver`是帮助我们获取代码符号的对象。我们可以构建一个如下所示的助手函数:

```
fun Resolver.*getSymbols*(cls: KClass<*>) =
    this.getSymbolsWithAnnotation(cls.qualifiedName.*orEmpty*())
        .*filterIsInstance*<KSClassDeclaration>()
        .*filter*(KSNode::validate)
```

`KSClassDeclaration`在源代码中表示为一个类或接口符号，`Resolver.getSymbolsWithAnnotation`是一个函数，它返回每一个具有目标注释的符号。

让我们简化这个问题，假设我们只需要生成一个工厂。我们可以用我们的注释得到所有的符号，如下所示:

```
val factory = resolver.*getSymbols*(AutoFactory::class).firstOrNull()
val elements = resolver.*getSymbols*(AutoElement::class).*toList*()
```

> 如果您对完整的实现感兴趣，请随意查看这里的！

## 科特林诗人

现在是时候将图形写入文件了。我们可以利用 Square 开发的一个很棒的工具——kotlin poet。科特林诗人也与 KSP 有很大的融合。只需首先添加依赖关系:

```
dependencies {    implementation 'com.squareup:kotlinpoet:1.11.0'
    implementation 'com.squareup:kotlinpoet-ksp:1.12.0'
}
```

然后创建一个函数来生成文件，如下所示:

```
private fun genFile(key: ClassName, list: List<ClassName>): FileSpec {
    val packageName = key.packageName
    val funcName = key.simpleName + "Factory"
    val enumName = key.simpleName + "Type"

    return FileSpec.builder(packageName, funcName)
        .addType(TypeSpec.enumBuilder(enumName)
            .*apply* {
                list.*forEach* {
                    addEnumConstant(it.simpleName.*uppercase*())
                }
            }
            .build())
        .addFunction(FunSpec.builder(funcName)
            .addParameter("key", ClassName(packageName, enumName))
            .returns(key)
            .beginControlFlow("return when (key)")
            .*apply* {
                list.*forEach* {
                    addStatement("${enumName}.${it.simpleName.*uppercase*()} -> %T()", it)
                }
            }
            .endControlFlow()
            .build())
        .build()
}
```

它有点长，但相当具有声明性，你可以看出 KotlinPoet 提供了一种比`StringBuilder`或其他手工作品更系统的方式来生成 Kotlin 代码。

生成`FileSpec`后，我们可以完成`process`函数中的整个流程，如下所示:

```
override fun process(resolver: Resolver): List<KSAnnotated> {
    val factory = ...
    if (factory != null) {
        val elements = ...
        genFile(
            factory.*toClassName*(),
            elements.*map*(KSClassDeclaration::toClassName)
        ).*writeTo*(codeGenerator, Dependencies(true))
    }
    return *emptyList*()
}
```

似乎很棒，但是编译器怎么知道处理器在哪里呢？处理器是由提供商创建的，下面是定义

```
fun interface SymbolProcessorProvider {fun create(env: SymbolProcessorEnvironment): SymbolProcessor
}
```

因此，我们可以在如下所示的提供程序中创建我们的处理器:

```
fun create(environment: SymbolProcessorEnvironment) =
    BuilderProcessor(
        environment.codeGenerator,
        environment.logger
    )
```

但仅此而已吗？编译器仍然不知道提供者在哪里。我们需要在一个名为`resources/META-INF/services/com.google.devtools.ksp.processing.SymbolProcessorProvider`的文件中注册我们的提供者的全名，包括包名，以便编译器链接。

## 应用模块

我们已经接近我们想要的了，现在我们可以在应用程序模块中使用处理器了。应用程序模块中的`build.gradle`文件如下所示:

```
plugins {
    id 'com.google.devtools.ksp'
}

sourceSets {
    main {
        java {
            srcDir "${buildDir.absolutePath}/generated/ksp/"
        }
    }
}dependencies {
    implementation project(":annotation")
    ksp project(":processor")
}
```

首先，应用 KSP 插件。并将注释和处理器模块声明为依赖关系。注意，对于处理器模块，我们需要使用`ksp`关键字。由于 KSP 目前对 IDE 来说相对较新，如果自动生成的文件已经创建，但是 IDE 不知道如何找到它们，就像`sourceSets`部分那样手动添加它。

```
@AutoFactory
interface Animal@AutoElement
class Dog : Animal@AutoElement
class Cat : Animal
```

最后，将注释添加到目标类中，然后重新构建项目，等待 KSP 为您生成文件，就这样！！

# 参考

【https://kotlinlang.org/docs/ksp-overview.html 

[https://medium . com/@ jintin/annotation-processing-in-Java-3621 CB 05343 a](/@jintin/annotation-processing-in-java-3621cb05343a)

[](https://github.com/Jintin/KFactory) [## GitHub-Jintin/KFactory:KFactory 是一个构建简单工厂模式的库，很容易利用…

### KFactory 是一个通过 ksp(Kotlin 符号处理)自动生成简单工厂类的库…

github.com](https://github.com/Jintin/KFactory)