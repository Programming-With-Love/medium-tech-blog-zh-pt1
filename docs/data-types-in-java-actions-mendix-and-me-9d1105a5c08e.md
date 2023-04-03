# Java 操作中的数据类型— Mendix 和 Me

> 原文：<https://medium.com/mendix/data-types-in-java-actions-mendix-and-me-9d1105a5c08e?source=collection_archive---------8----------------------->

![](img/5c41e807f362b3a0fd66a89c431ef16f.png)

时不时地，你会到达你想要更多的点。这通常是您开始使用自己的 Java 操作的时候。要做到这一点，您必须将数据/参数传递给一个 Java 动作，然后获取数据。Java 中的哪些数据类型对应 Mendix 中的哪些数据类型？尤其是当你处理未定义的对象时，你必须知道哪些数据可以放入哪些属性中。下面是 Mendix 中的哪些数据类型在作为参数传递时最终出现在 Java 动作中的概述。

# 参数类型

[https://gist.github.com/gAndy84/0e9da00365666ba4c8fb1c69ee0e2073](https://gist.github.com/gAndy84/0e9da00365666ba4c8fb1c69ee0e2073)

如你所见，基本上没有使用原始数据类型。如果使用基本数据类型，如果要将它们返回或保存到属性中，可能需要创建一个对象。如果你知道 Mendix 期望什么，这当然没问题。

在 Mendix 为域模型中的实体生成的代理类上，整个事情看起来很相似。以下属性类型在代理类中具有以下对应关系。

# 代理属性的类型

[https://gist.github.com/gAndy84/13b311e7d126cf75b40cea8cdcd7c662](https://gist.github.com/gAndy84/13b311e7d126cf75b40cea8cdcd7c662)

有趣的是，整型和长整型是有区别的，而传递的参数却不是这样。所以你可能要小心了。

如果您使用 IMendixObject 对象(当您为任何对象创建 Java 操作时，您总是这样做)，那么在这个对象上使用的数据类型与在代理类上使用的数据类型相同。

*原文于 2020 年 2 月 27 日在* [*用德语发表 https://mendixamme . de*](https://mendixandme.de/index.php/2020/02/27/datentypen-in-java-actions/)*。*