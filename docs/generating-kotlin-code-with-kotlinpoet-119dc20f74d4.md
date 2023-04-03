# 用 KotlinPoet 生成 Kotlin 代码

> 原文：<https://medium.com/square-corner-blog/generating-kotlin-code-with-kotlinpoet-119dc20f74d4?source=collection_archive---------0----------------------->

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

Java 代码生成已经成为简化库代码的流行解决方案。 [Dagger](https://github.com/google/dagger/) 生成接口实现， [Butter Knife](https://github.com/JakeWharton/butterknife/) 生成 Android UI 样板， [Wire](https://github.com/square/wire) 生成数据二进制编码的值类实现。

尽管 Kotlin 与 Java 有很强的互操作性，但为这些库生成的 Java 代码可能会让人感到陌生和违反惯例，因为它们面向 Java 消费者，并且缺少 Kotlin 的特性。

今天我们很高兴地宣布 [KotlinPoet](http://github.com/square/kotlinpoet) ，一个用于生成 Kotlin 代码的库！

Square 的代码生成历史始于 JavaWriter——一个需要自顶向下发射的线性代码生成器。我们与 Google 的 Dagger 团队合作开发了它的继任者 [JavaPoet](https://github.com/square/javapoet) ，它在提供构建器和生成代码的不可变模型方面进一步发展了这个概念。KotlinPoet 建立在 JavaPoet 的成功之上，为创建 Kotlin 提供了类似的模型:

```
val greeterClass = ClassName.get("", "Greeter")
val kotlinFile = KotlinFile.builder("", "HelloWorld")
    .addType(TypeSpec.classBuilder("Greeter")
        .primaryConstructor(FunSpec.constructorBuilder()
            .addParameter(String::class, "name")
            .build())
        .addProperty(PropertySpec.builder(String::class, "name")
            .initializer("name")
            .build())
        .addFun(FunSpec.builder("greet")
            .addStatement("println(%S)", "Hello, \$name")
            .build())
        .build())
    .addFun(FunSpec.builder("main")
        .addParameter(ArrayTypeName.of(String::class), "args")
        .addStatement("%T(args[0]).greet()", greeterClass)
        .build())
    .build()
```

上述代码产生以下 Kotlin:

```
class Greeter(name: String) {
  val name: String = name fun greet() {
    println("Hello, $name")
  }
}fun main(args: Array<String>) {
  Greeter(args[0]).greet()
}
```

与生成 Java 相比，生成 Kotlin 还有一个优势:JavaScript 和 native 可以作为第一方编译目标。这允许您为多个平台使用相同的工具和相同的生成代码。

KotlinPoet 目前是一个早期版本。虽然还没有涵盖所有的语言特性和语法，但是已经足够开始并公开了。我们期待着看到你用这个库构建的东西！

这篇文章总结了 Square 的“ [Square 开源♥s·科特林](/square-corner-blog/square-open-source-loves-kotlin-c57c21710a17)”系列。