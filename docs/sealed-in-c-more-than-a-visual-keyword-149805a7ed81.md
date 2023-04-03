# 用 C#封装——不仅仅是一个可视的关键字

> 原文：<https://medium.com/quick-code/sealed-in-c-more-than-a-visual-keyword-149805a7ed81?source=collection_archive---------0----------------------->

![](img/849523c2878bf7d9b794b743c82601a8.png)

## 让我们来谈谈 C#中的 sealed 关键字，以及编译器为什么有时会因为它而进行微优化。这意味着关键字不仅从可读性的角度来看是一个好习惯，而且还可以提高性能。

让我首先解释一下继承在编译器层面上是如何工作的。基本上每个从父类继承的类都继承了所有的属性，重要的是它也继承了所有的方法。方法只是程序集中代码执行指针应该跳转到的内存位置。当我们作为程序员定义虚函数时，编译器在后端生成一个 vtable。不需要太多的细节，当我们调用一个虚函数时，它会跳转到那个 vtable 入口。之后，我们将跳转到 vtable 条目指向的位置。顺便说一句，vtable 确实会占用一点空间，这是程序员并不总是意识到的。

当使用**密封的**关键字时，如果编译器有信心进行直接方法调用，从而优化并完全跳过 vtable 查找。这不会给你带来任何巨大的性能提升，但当我在游戏引擎的热路径中持续使用 sealed 时，它确实提高了性能。除此之外，明确表示该类不打算被覆盖并删除任何假设也是一个好的做法。这既是为了项目未来的程序员，当然也是为了编译器。

您提供给编译器的这些额外信息有助于它更好地理解您的代码。我将在编译器方面做一些额外的深入研究，但是对于大多数读者来说，只要记住在你觉得合适的地方使用 sealed 关键字就可以了。

![](img/0ff55b69fe9369ee4398a591cf3f0951.png)

Photo by [Alexander Sinn](https://unsplash.com/@swimstaralex?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/binary?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

## 编译器深潜

你可以在 clang 编译器(C++) [这里](https://github.com/llvm-mirror/clang/blob/0e2d28ae3ba7407eb4c0b777f63e237013608261/lib/CodeGen/CGClass.cpp#L2676)中看到它的具体实现，值得注意的是所有编译器的实现可能略有不同。然而，总的想法是交叉编译器兼容。这种技术也在《毁灭战士》中使用过，fabian 巧妙地解释了他们是如何在[展开循环](https://fabiensanglard.net/doom3/index.php)的情况下只进行了很少的 vtable 查找就成功了。Unity 也有一篇关于这一点的[好文章](https://blogs.unity3d.com/2016/07/26/il2cpp-optimizations-devirtualization/)，并用一个例子来展示改进。

![](img/0b0ca0ba6d6a4d1f23859c04129ff552.png)

Cover image by [**Karolina Grabowska**](https://www.pexels.com/@karolina-grabowska?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) from [**Pexels**](https://www.pexels.com/photo/crop-man-sealing-cardboard-container-with-tape-4498142/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels).