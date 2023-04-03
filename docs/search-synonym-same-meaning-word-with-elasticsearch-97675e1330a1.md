# 用 Elasticsearch 搜索同义词(相同意思的单词)

> 原文：<https://medium.easyread.co/search-synonym-same-meaning-word-with-elasticsearch-97675e1330a1?source=collection_archive---------1----------------------->

## 不再有因为*俚语*和*本地语言*单词而产生的空结果

在我的工作场所，我们的应用程序有一个功能，可以从搜索框中搜索数据。它工作得非常好，直到我们检查数据，许多用户从搜索中得到空的结果。其中一个原因是他们输入了这个词的俚语或不同的语言版本，而我们并没有把这些储存在我们的数据库里。

![](img/572767b84f04e0caebcb42dd1b1131fb.png)

Photo by [Markus Winkler](https://unsplash.com/@markuswinkler?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在我的国家印度尼西亚，有许多地方语言和俚语。例如`**Bakso**`和`**Baso**`意思相同，就是`**Meatball**`。除了使用当地语言，我们的用户也开始使用英文关键词进行搜索，比如`**apple**`，而不是`**apel**`，尽管我们的应用是针对印度尼西亚人的。

有许多解决方案可以解决我们的问题，我缩小了解决方案的范围，使我们的代码能够检索带有同义词的单词。我知道对于非母语，我们可以通过某种本地化来解决，但我认为这有点过分，因为我们的用户只搜索一点非母语。

![](img/28a53807fa3beefe97d7074585d34967.png)

elasticsearch logo.

在我们的数据库中，我们使用 [Elasticsearch](https://www.elastic.co/) 来索引数据，这样更容易搜索，幸运的是它有一个叫做`**Synonym Token Filter**`的特性。我很高兴发现 Elasticsearch 原生支持这种功能，因为如果我们需要另一个数据库或应用程序来修复我们的空结果错误，这将更加困难。

我将告诉你我是如何实现这个特性的。要使它在我们的应用程序中工作，需要完成一些步骤，有:

## 1.添加同义词过滤器

我们需要更新应用程序的当前索引，以包含一个新的`**synonym**`过滤器。在现有的`**filter**`键中添加一个新值:

请注意有一个`**synonym**`一个有 3 个键的对象，`**type**`有`**synonym**`值，`**lenient**`有`**true**`值，`**synonyms**`有一个字符串数组作为它的值。

**类型** 我认为这是不言自明的，它告诉我们这个对象是同义词类型。

**宽松** 基于弹性搜索文档:

```
If true ignores exceptions while parsing the synonym configuration. It is important to note that only those synonym rules which cannot get parsed are ignored.
```

该键的默认值为 false，因此我将其设置为 true，以防止任何应用程序错误，即使同义词过滤器出错。

**同义词** 这是我们为词语配置其同义词的地方。弹性搜索支持两种格式，即 Solr 和 WordNet，就我而言，我更喜欢使用 Solr 格式。

```
# Blank lines and lines starting with pound are comments.

# Explicit mappings match any token sequence on the LHS of "=>"
# and replace with all alternatives on the RHS.  These types of mappings
# ignore the expand parameter in the schema.
# Examples:
i-pod, i pod => ipod,
sea biscuit, sea biscit => seabiscuit

# Equivalent synonyms may be separated with commas and give
# no explicit mapping.  In this case the mapping behavior will
# be taken from the expand parameter in the schema.  This allows
# the same synonym file to be used in different synonym handling strategies.
# Examples:
ipod, i-pod, i pod
foozball , foosball
universe , cosmos
lol, laughing out loud

# If expand==true, "ipod, i-pod, i pod" is equivalent
# to the explicit mapping:
ipod, i-pod, i pod => ipod, i-pod, i pod
# If expand==false, "ipod, i-pod, i pod" is equivalent
# to the explicit mapping:
ipod, i-pod, i pod => ipod

# Multiple synonym mapping entries are merged.
foo => foo bar
foo => baz
# is equivalent to
foo => foo bar, baz
```

这些文字可以直接写入`**synonyms**`键，但是如果列表太长，建议我们使用`**synonyms_path**`键，并传递我们的`**txt**` 文件的位置。

```
"filter" : {
    "synonym" : {
          "type" : "synonym",
          "synonyms_path" : "analysis/synonym.txt"
    }
}
```

**2 .创建文档属性分析器**

在我们创建过滤器之后，我们需要创建一个使用过滤器的`**analyzer**`。

**2a。将过滤器添加到现有分析仪中**

每个文档属性只有一个 analyzer，如果我们的文档属性已经有了一个 analyzer，您需要在里面添加`**synonym**`过滤器。

**3 .更新弹性搜索索引设置**

用 PUT 方法向您的弹力搜索主机请求`**/application/_settings**`，并发送带有`**synonym**`过滤、附加`**analyzer**`过滤值的请求体。

**4 .完成！**

现在，您可以搜索具有关键字同义词的项目！请注意，所有这些步骤都只是一个简单的例子，如果您想了解更多关于弹性搜索功能的细节，您可以直接阅读他们的官方[文档](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/analysis-synonym-tokenfilter.html)。快乐编码！🖖

# 参考

1.  [同义词的同义词| Thesaurus.com](https://www.thesaurus.com/browse/synonym)
2.  [同义词标记过滤编辑](https://www.elastic.co/guide/en/elasticsearch/reference/6.8/analysis-synonym-tokenfilter.html)