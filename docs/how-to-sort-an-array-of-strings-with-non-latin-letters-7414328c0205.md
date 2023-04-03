# 如何对非拉丁字母的字符串数组进行排序？

> 原文：<https://medium.com/quick-code/how-to-sort-an-array-of-strings-with-non-latin-letters-7414328c0205?source=collection_archive---------1----------------------->

![](img/f3303cfe9eccbba35b485ff0465a3d00.png)

Photo by [Ryoji Iwata](https://unsplash.com/photos/3vk8Dgkd_Sc?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/words-sentence?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

JavaScript 有一个原生的排序方法，你可以用 array.sort()来做，它会把数组按字母顺序排序。此外，您可以提供自定义排序功能。

但是如果你要排序一个非 ASCII 字符的数组，比如*【'ą'、'ó'、'ó'、'ż'、'ź'、' e']* ，你会收到一个 *["e "、"ó"、"ą"、"ó"、"ź"、"ż]]*。这是因为排序功能只适用于英文字母。

幸运的是，有两种方法可以克服这种行为 [localeCompare](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/String/localeCompare) 和 [Intl。ECMAScript 国际化 API 提供的 Collator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Collator) 。

## 使用`localeCompare()`

## 使用`Intl.Collator()`

因此，当您使用非英语语言的字符串数组时，请记住使用这些方法来避免意外排序。

感谢您的宝贵时间！