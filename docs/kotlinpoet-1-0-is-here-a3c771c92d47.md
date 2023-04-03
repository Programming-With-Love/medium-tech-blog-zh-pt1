# KotlinPoet 1.0 来了！

> 原文：<https://medium.com/square-corner-blog/kotlinpoet-1-0-is-here-a3c771c92d47?source=collection_archive---------4----------------------->

![](img/898367266d5252c9203e7cf3079c62d4.png)

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

KotlinPoet 是一个 Kotlin API 集合，它使得生成`.kt`文件变得容易。它的灵感来自于 [JavaPoet](https://github.com/square/javapoet) ，这是一个 Java 代码生成库，支持许多广泛使用的框架，如 [Dagger](https://github.com/google/dagger) 。

KotlinPoet 已经存在一年多了，被许多流行的图书馆使用，比如 [Moshi](https://github.com/square/moshi) 和[sqldeleght](https://github.com/square/sqldelight)。它很好，经过了战斗的考验，已经准备好了，今天我们要宣布 KotlinPoet 1.0！🎉

## 你好，世界，科特林诗人！👋

这里有一个来自 KotlinPoet 的 [README](https://github.com/square/kotlinpoet/blob/master/README.md) 页面的`HelloWorld`文件:

```
class Greeter(val name: String) {
    fun greet() {
        println("Hello, $name")
    }
}fun main(vararg args: String) {
    Greeter(args[0]).greet()
}
```

这是用 KotlinPoet 生成它的代码:

```
val greeterClass = ClassName("", "Greeter")
val file = FileSpec.builder("", "HelloWorld")
    .addType(TypeSpec.classBuilder("Greeter")
        .primaryConstructor(FunSpec.constructorBuilder()
            .addParameter("name", String::class)
            .build())
        .addProperty(PropertySpec.builder("name", String::class)
            .initializer("name")
            .build())
        .addFunction(FunSpec.builder("greet")
            .addStatement("println(%P)", "Hello, \$name")
            .build())
        .build())
    .addFunction(FunSpec.builder("main")
        .addParameter("args", String::class, VARARG)
        .addStatement("%T(args[0]).greet()", greeterClass)
        .build())
    .build()file.writeTo(System.out)
```

这里需要指出几件事:

*   规格。 KotlinPoet 使用一组 Spec 类对文件结构建模，每个 Spec 类代表一个特定的 Kotlin 构造。每个规范都有一个关联的构建器类，这使得配置规范变得很简单。所有的规格最终都应该被添加到一个`FileSpec`，它代表一个`.kt`文件。
*   **类型。引用类型很容易，因为 KotlinPoet 的大部分 API 都经过优化，可以与 Kotlin 的`KClass`协同工作。还有几个类可以让你介绍自己的类型，比如`ClassName`、`LambdaTypeName`、`ParameterizedTypeName`等。**
*   **自定义格式说明符。KotlinPoet 附带了许多格式说明符，它们的工作方式类似于`%s`和`%d`。例如，`%S`将把它的参数变成一个双引号字符串，而`%L`可以把整个规范作为一个参数。**
*   **输出**。配置好规格后，调用`FileSpec`上的`writeTo()`，将模型输出到选择的目的地，支持的选项有`kotlin.Appendable`、`java.io.File`和`java.nio.file.Path`。

KotlinPoet 可以生成扩展和单表达式函数、可空类型、数据类、`expect` / `actual`定义等等！要了解更多关于 KotlinPoet API 的信息，请查看 [KDoc](https://square.github.io/kotlinpoet/1.x/kotlinpoet/kotlinpoet/com.squareup.kotlinpoet/index.html) 和 [README](https://github.com/square/kotlinpoet/blob/master/README.md) 。

现在是代码生成时间！