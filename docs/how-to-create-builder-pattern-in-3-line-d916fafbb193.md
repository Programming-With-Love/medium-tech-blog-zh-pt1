# 使用 Lombok 的实现生成器模式

> 原文：<https://medium.easyread.co/how-to-create-builder-pattern-in-3-line-d916fafbb193?source=collection_archive---------2----------------------->

![](img/5e035114f0ea902259d7730430927201.png)

本文将重点讨论使用 Lombok 的构建器模式。

**引入构建器模式**

**Builder 模式**是 Java 中的**设计模式**之一，它通过自定义类型和参数对象来减少构造函数或方法调用所需的参数数量。

**什么是龙目岛？**

Lombok 是减少模型/数据对象样板代码的依赖项。它可以生成 getters、setters、builder 模式和。等等。使用批注“@”。

**实现构建器模式**

对于我们的例子，我们将把构建器放在动物类中。看起来是这样的。

然后，创建 **AnimalBuilder** 类，如下所示:

这是设置生成器内容的源代码:

**使用 Lombok 实现构建器模式**

为了使用 Lombok 实现**构建器模式，我们需要在 Maven 项目中运行这个源代码，为了实现 Lombok，必须向 pom.xml 添加一个依赖项**

然后创建**动物类，**这样:

这是设置生成器内容的源代码:

来自两个源输出:

```
Animal(species=Kucing, ras=Anggora)
```

GitHub 来源:

[](https://github.com/fascalsj/builderjava) [## fascalsj/builderjava

### 通过在 GitHub 上创建帐户，为 fascalsj/builderjava 开发做贡献。

github.com](https://github.com/fascalsj/builderjava) 

感谢:[https://medium.com/@a.adendrata](https://medium.com/@a.adendrata)