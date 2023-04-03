# JavaPoet 简介

> 原文：<https://medium.com/square-corner-blog/introducing-javapoet-f9a41435e396?source=collection_archive---------0----------------------->

## Square 有一个用于生成 Java 代码的新库。

*由* [撰写*杰西·威尔逊*T5*。*](https://medium.com/u/dee2b4f5bec4?source=post_page-----f9a41435e396--------------------------------)

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

我过去讨厌自动生成的代码。JSP、EJB 和协议缓冲区的早期实现产生了大量格式错误的文件，其中充斥着警告和完全限定的类型名:

```
*/***
 ** <code>optional string error_text = 2;</code>*
 **/*
    **public** Builder **setErrorText(**
        java**.**lang**.**String value**)** **{**
      **if** **(**value **==** **null)** **{**
  **throw** **new** **NullPointerException();**
**}**
bitField0_ **|=** 0x00000002**;**
      errorText_ **=** value**;**
      onChanged**();**
      **return** **this;**
    **}**
```

但是事情会变好的。由于 Java 强大的[注释处理](http://hannesdorfmann.com/annotation-processing/annotationprocessing101/)功能，将生成的代码集成到项目中变得更加容易。测试代码生成器已经被 Google 的[编译测试](https://github.com/google/compile-testing)库驯服了。今天，由于[自动赋值](https://github.com/google/auto)、[匕首](http://square.github.io/dagger/)、[黄油刀](http://jakewharton.github.io/butterknife/)和[钢丝](https://github.com/square/wire)中生成的代码，方块移动得更快了。

不幸的是，在这些库中生成代码的代码仍然很笨拙。例如，Dagger 从上到下编写每个生成的文件，这意味着它需要将准备导入作为一个笨拙的单独步骤。它模拟了。java 文件作为一个字符串，不理解它的结构。

今天，我迫不及待地宣布 JavaPoet，JavaWriter 的继任者。javaPoet 用类型、方法和字段对. Java 文件建模，并自动执行导入。像这样生成“Hello World ”:

```
MethodSpec main **=** MethodSpec**.**methodBuilder**(**"main"**)**
    **.**addModifiers**(**Modifier**.**PUBLIC**,** Modifier**.**STATIC**)**
    **.**returns**(void.**class**)**
    **.**addParameter**(**String**[].**class**,** "args"**)**
    **.**addStatement**(**"$T.out.println($S)"**,** System**.**class**,** "Hello, JavaPoet!"**)**
    **.**build**();**TypeSpec helloWorld **=** TypeSpec**.**classBuilder**(**"HelloWorld"**)**
    **.**addModifiers**(**Modifier**.**PUBLIC**,** Modifier**.**FINAL**)**
    **.**addMethod**(**main**)**
    **.**build**();**JavaFile javaFile **=** JavaFile**.**builder**(**"com.example.helloworld"**,** helloWorld**)**
    **.**build**();**javaFile**.**emit**(**System**.**out**);**
```

在 GitHub 上获取[JavaPoet](https://github.com/square/javapoet)。

[](/@swankjesse) [## 杰西·威尔逊

### 安卓和笑话。

medium.com](/@swankjesse)