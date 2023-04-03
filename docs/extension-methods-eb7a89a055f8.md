# 用扩展方法扩展您的能力——Flutter💙

> 原文：<https://medium.com/google-developer-experts/extension-methods-eb7a89a055f8?source=collection_archive---------3----------------------->

## 我们都喜欢做这项工作的短代码。所以 dart 提供了一个很棒的特性，叫做扩展方法，你可以在不同的数据类型上使用它！

![](img/b5b39daf35f2dabc829ca2750ca8c5f8.png)

谁不喜欢小巧、短小、可爱的工作代码呢？为了完成工作，我们都写过又长又乱的代码！但是，Dart 知道这种痛苦，因此我们有了**扩展方法**！您可能会觉得它与我们普通的用户自定义函数很相似，实际上非常相似！

我们已经看到了下面的语法:

```
Text(capitalFirstChar('hello world'))
...String capitalFirstChar(String data) {
  return data.split(" ").map((str) => str[0].toUpperCase() + str.substring(1)).join(" ");
}
```

所以这是一个以 String 为参数，将每个单词的首字母大写，然后返回新字符串的方法。

**然而，如果你能像下面这样做呢:**

```
Text('hello world. how are you'.capitalFirstChar())
```

这可以使用扩展方法来实现！

**扩展方法是 Dart 中提供的特殊方法，可以直接用在数据类型上执行某些操作！**

要在 String 数据类型上创建扩展，您可以对上面的`capitalFirstChar`方法执行以下操作:

```
extension StringExtension on String {
  String capitalFirstChar() {
    return this.split(" ").map((str) => str[0].toUpperCase() + str.substring(1)).join(" ");
  }
}
```

这将创建一个可用于字符串数据类型的扩展方法。在这里，您可以使用 **this 关键字访问调用该方法的字符串！**

现在，要访问这个方法，您只需要导入创建了上述函数的文件，并将其用于任何字符串！

```
Text('hello world. how are you'.capitalFirstChar()),
```

下面举个小例子供大家参考:

## string _ 扩展名. dart

## main .镖:

请随意从 [GitHub](https://github.com/AbhishekDoshi26/extension_example) 中克隆这个库！

# 希望你喜欢这篇文章！

如果你喜欢，你可以 [**请我喝杯咖啡**](https://www.buymeacoffee.com/abhishekdoshi26) **！**

[![](img/1acaf66ccf78649c48c34bfd7130388a.png)](https://www.buymeacoffee.com/abhishekdoshi26)

# 不要忘记通过以下方式与我联系:

*   [**Instagram**](https://www.instagram.com/abhishekdoshi26/)
*   [**推特**](https://twitter.com/AbhishekDoshi26)
*   [**领英**](https://www.linkedin.com/in/AbhishekDoshi26)
*   [**GitHub**](https://github.com/AbhishekDoshi26)

> 不要停止，直到你在呼吸！💙
> -阿布舍克·多希