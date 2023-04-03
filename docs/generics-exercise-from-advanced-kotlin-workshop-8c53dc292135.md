# 来自高级科特林研讨会的泛型练习

> 原文：<https://blog.kotlin-academy.com/generics-exercise-from-advanced-kotlin-workshop-8c53dc292135?source=collection_archive---------0----------------------->

![](img/b62101ac432613b2f47dc73ed0bfa905.png)

在我的高级 [Kotlin 工作坊](https://www.kt.academy/)上，我们在每章之后做代码练习。这个练习就是这样诞生的。今天我决定与大家分享它，因为它不仅是一个伟大的练习，也是一个需要分享的重要知识。享受:)

[![](img/0742a8ad0cfd3851db2d28061bf6f214.png)](https://leanpub.com/effectivekotlin/c/3YYtCtqCC6a4)

当我们需要表示结果时，我们通常决定使用密封类来表示它们:

```
**sealed class** Result<T, R>
**class** Success<T, R>(**val resp**: T): Result<T, R>()
**class** ErrorResult<T, R>(**val error**: R): Result<T, R>()
```

这种符号很容易传递和处理:

```
*getNews* **{** result **-> 
    when**(result) {
        **is** Success -> *showNews*(result.**resp**)
        **is** ErrorResult -> *showError*(result.**error**)
    }
**}**
```

尽管它的泛型行为设计得非常糟糕。我相信`Success("**Message"**)`也应该既是`Result<String, Error>`又是`Result<Any, Error>`:

```
**val** s = Success(**"Message"**)
**val** r1: Result<String, Error> = s
**val** r2: Result<Any, Error> = s
```

同样的应该是`Result<String, String>`和`Result<Any, String>`:

```
**val** s = Success(**"Message"**)
**val** r1: Result<String, String> = s
**val** r2: Result<Any, String> = s
```

错误也是如此:

```
**val** e = ErrorResult(Error())
**val** r1: Result<String, Error> = e
**val** r2: Result<Any, Error> = e
**val** r3: Result<String, Any> = e
**val** r4: Result<Any, Any> = e
```

此外，我认为`Success<String>`也应该是`Success<CharSequence>`和`Success<Any>`:

```
**val** s = Success(String())
**val** r1: Success<CharSequence> = s
**val** r2: Success<Any> = s
```

你能做到吗？

你可以在网上玩这个问题:

[](https://try.kotlinlang.org/#/UserProjects/ui3j0bjgv9p0g29uaic1gmn35v/u68rfd8sf07fs2j1sar1isnujv) [## 试试 Kotlin:挑战泛型

### 在浏览器中尝试 Kotlin。

try.kotlinlang.org](https://try.kotlinlang.org/#/UserProjects/ui3j0bjgv9p0g29uaic1gmn35v/u68rfd8sf07fs2j1sar1isnujv) [![](img/018370a2476e1ce49e6d3299428b4f2a.png)](https://www.kt.academy/#workshops-offer)

在工作坊中，我们以泛型和 Kotlin 类型系统的知识为基础。很难用网上的资源来代替这个，但是我想给你一些你可以依据的东西。对于泛型，您可以使用以下文章:

[](/kotlin-generics-variance-modifiers-36b82c7caa39) [## 科特林通用方差修改器

### 即使对于有经验的开发人员来说，泛型有时也令人困惑。最后还是简单明了吧。

blog.kotlin-academy.com](/kotlin-generics-variance-modifiers-36b82c7caa39) [](/understanding-kotlin-limitations-for-type-parameter-positions-15527b916034) [## 了解 Kotlin 对类型参数位置的限制

### Kotlin 方差修饰符对类型参数的使用施加了限制。协变类型参数(无修饰符)…

blog.kotlin-academy.com](/understanding-kotlin-limitations-for-type-parameter-positions-15527b916034) 

对于 Kotlin 型系统，我建议使用以下材料:

[](/kotlins-nothing-its-usefulness-in-generics-5076a6a457f7) [## 科特林的虚无:它在泛型中的有用性

### 本文探讨了 Kotlin 的 Nothing 类型在泛型中的用途。我们来看看它与 Java 的关系。

blog.kotlin-academy.com](/kotlins-nothing-its-usefulness-in-generics-5076a6a457f7) 

如果你不知道该做什么，不要盯着代码看，争论并检查不同的改变如何影响编译器对待你的类型。这是最好的学习方法之一。

我相信你自己能找到答案。您需要应用于初始代码的更改非常小。如果你放弃了，你想看答案，可以在这里找到[。别担心，我总是在工作坊上做一些额外的练习；)](https://gist.github.com/MarcinMoskala/29d0095bb3b8881d6c0e7c1d58d96a8d)

你需要 Kotlin 工作室吗？访问我们的网站[看看我们能为您做些什么。](https://www.kt.academy/)

了解卡帕头最新的重大新闻。学院，[订阅时事通讯](https://kotlin-academy.us17.list-manage.com/subscribe?u=5d3a48e1893758cb5be5c2919&id=d2ba84960a)，[观察推特](https://twitter.com/ktdotacademy)并在媒体上关注我们。

[![](img/3146970f03e44cb07afe660b0d43e045.png)](http://eepurl.com/diMmGv)

## 单击👏说“谢谢！”并帮助他人找到这篇文章。

请注意，如果您按住鼓掌按钮，您可以留下更多的掌声。

![](img/f36a792ac0eb95fc577e6f4125dba956.png)