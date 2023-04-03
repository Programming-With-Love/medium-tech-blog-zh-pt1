# 用 Kotlin(非 Java)方式编写 Kotlin

> 原文：<https://medium.com/google-developer-experts/writing-kotlin-the-kotlin-non-java-way-c1a636b9b62a?source=collection_archive---------1----------------------->

我开始编写真正的 Kotlin 代码已经有几个星期了，我说的真正的 Kotlin 代码是指在我在 [Shopify](https://medium.com/u/bab76dfc19b0?source=post_page-----c1a636b9b62a--------------------------------) 担任 Android 开发人员的头几个星期里，超越 Kotlin 官方网站上的 [koans](https://try.kotlinlang.org/) (它们相当不错，你应该去看看)编写库和重新编写一些组件。

虽然我可以说，现在我很舒服，享受这种语言，并慢慢地对它产生了无条件的爱，因为它很容易接受新的东西，但我真的觉得这种学习速度在开始时并不存在。

这有很多原因，其中一个原因是对**表达式**和**语句**之间的区别理解不太清楚，我特别要说的是理解这些概念在 **Java** 和 **Kotlin 中呈现它们自己的方式。**

# **报表**

简而言之，当编程或与计算机交互时，语句是我们给它的命令，以便它执行任何类型的动作/操作。

```
System.out.println("This is a statement");
```

上面的 Java 语句会告诉程序将字符串打印到控制台，这是唯一会发生的事情。

这使我们对陈述的定义稍作修改，如下所示:

> 我们给程序的一条指令，它不返回任何值，并且总是在它的封闭块的顶层。

Java 是一种语言，其中几乎所有的东西都是一个语句，我们通过将不同的语句组合在一起来指示我们的代码做我们想要做的事情。

看一下我们通常在 Android 中做的一个常见场景，例如在将字符串或/和 Parcelable 设置为片段的参数之前，将它放入一个包中，我们将按以下方式进行:

```
Bundle args = new Bundle();
args.putString("key", "value");
args.putParcelable("parcelableKey", pacelableObject);Fragment fragment = new FragmentX();
fragment.setArguments(args);
```

# 公式

表达式也是我们在编写程序时给出的指令，但它们不同于语句，因为表达式可以**返回值**和**可以是表达式的一部分或接受其他表达式。**

在 Java 中，有 4 种类型的表达式:

```
**Assignment expressions:** Such as a+=b, or simply a=2**Postfix or infix operations:** Such as --a, a++**Methods invocations:** calling a method with or no return**Object Creations:** Such as new ObjectA()
```

向前看，上面对语句和表达式的解释似乎非常清楚和明显。

现在，你可能会问自己，这与编写好的 Kotlin 代码有什么关系？

这个问题的答案是，Java 有 4 种类型的表达式，如本文前面所示，但另一方面，Kotlin 在设计时考虑到了大量使用表达式和函数式编程(查看 Dan Lew 的更详细的文章[此处](http://blog.danlew.net/2017/07/27/an-introduction-to-functional-reactive-programming/))作为编写更简洁代码的方式。

用非 Java 方式编写 Kotlin(至少在本文的上下文中)意味着要理解这种语言中几乎到处都有表达式。

这意味着我们应该尝试摆脱 Java 编写多条语句的方式，使用我前面提到的表达式的一个属性，即组合并成为其他表达式的一部分的能力。

让我们从一个简单的函数示例开始，该函数获取年龄并检查这个人是否达到饮酒年龄(假设 18 岁达到饮酒年龄，至少在世界的某些地方是这样)。

在 Java 中，它看起来像这样:

```
public String getDrinkingAuthorization(int age) {
   if(age >= 18){
      return "Authorized";
   }
   else{
      return "Not Authorized";
   }}
```

通过使用 Android Studio“从 Java 转换到 Kotlin”工具，它将生成以下代码:

```
fun getDrinkAuthorization(age: Int): String{
   if(age >= 18){
      return "Authorized"
   }
   else{
      return "Not Authorized"
   }
}
```

如果你看一下这个函数的两个版本，它们看起来绝对是一样的，如果你的愿望是把你的代码放在一个 **kt** 文件中，这应该是你用 Kotlin 写第一个代码的时候了。

然而，我相信我们做我们所做的是出于爱；正因为如此，我们应该不断努力提高。上面的代码可以用 Kotlin 的方式重写，如果我们更喜欢**表达式**而不是**语句**:我们从条件的每个部分中去掉 return 关键字的重复，并对整个表达式使用单个 return。

看起来会像这样:

```
**fun** getDrinkAuthorization(age: Int): String{
   return if(age >=18) "Authorized" else "Not Authorized";
}
```

**注意:**如果你注意的话，在上面的代码片段中, **if** 和 **else** 没有任何块体，因为我们只返回一个表达式，但是如果我们想在返回我们想要的值之前做更多的事情，我们可以在一个块中提供表达式，你唯一需要记住的是**你返回的值应该总是块中的最后一个表达式。**像这样:

```
**fun** getDrinkAuthorization(age: Int): String{
   return if(age >=18){
          //..do some funky stuff
          "Authorized"
      }
      else{
         //do some funky stuff here too
         "Not Authorized"
      }
}
```

以我们之前使用的捆绑包为例，使用 Java 到 Kotlin 转换器，我们将得到如下所示的代码:

```
val bundle = Bundle()
bundle.putString("key", "value")
bundle.putParcelable("parcelableKey", parcelable)val fragment = FragmentX()
fragment.arguments = bundle
```

通过利用 Kotlin 中的表达式，我们可以用许多不同的形式编写代码。下面是我们编写这段代码的一种方法:

```
**val** fragment = TestFragment().*apply* **{** *arguments* = Bundle().*apply***{
        this**.putString(**"Key"**,**"value"**)
        **this.**putParcelable("parcelableKey", parcelable)
    **}
}**
```

上面的代码片段使用了一个 Kotlin 标准库函数。

**apply** 函数基本上可以应用于任何对象，它的作用是将调用对象传递给可以用 **this** 关键字访问的块体，并在块内完成操作后返回一个与调用对象同类型的对象。

这是另一个话题，我不会说太多细节，因为有一个由[托梅克·波兰斯基](/@tpolansk?source=post_header_lockup)写的很棒的帖子，可以在[这里](/@tpolansk/the-difference-between-kotlins-functions-let-apply-with-run-and-else-ca51a4c696b8)找到。

# 结论

Java 到 Kotlin 转换器是开始将代码库转移到 Kotlin 的一个很好的方式，但是理解简单的概念，比如表达式和语句之间的区别，从长远来看，可以帮助您编写更好的 Kotlin 代码。

像标准库函数这样的东西在开始时并不是很直观。

我建议你首先用 java 方式编写代码，然后不断试验和学习新的函数，直到得到你想要的结果。

> 感谢您阅读我在学习科特林的过程中做的一些笔记。
> 
> 请在评论区留下你的想法或问题。让我们继续对话，一起学习。
> 
> 如果你觉得这篇文章很有趣，**与你的朋友、同事分享**，并通过点击绿色小心脏来推荐它

**感谢@**[**Francisco cavedon**](http://By using the Android Studio “Convert from Java to Kotlin” tool, it would generate the following code:)**，** [**丽贝卡·弗兰克斯**](https://medium.com/u/3f9b9c30bec7?source=post_page-----c1a636b9b62a--------------------------------) **和** [**罗萨里奥·佩雷拉·费尔南德斯**](https://medium.com/u/60764aad5eb3?source=post_page-----c1a636b9b62a--------------------------------) **抽出时间帮我审阅这篇文章。**

下次见，

分米