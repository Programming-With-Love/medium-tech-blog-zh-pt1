# 动态角度形式

> 原文：<https://medium.com/quick-code/dynamical-angular-forms-b36769859cb?source=collection_archive---------0----------------------->

## 角度指南

## 动态创建表单

角形控制和角形群给了我们许多机会。主要的一个是动态地改变问题和验证。

有时取决于用户必须回答几个附加问题的答案。

我们有一个简单的表格。

![](img/30b52d734185a6aba69e6b8de5e33773.png)

Form

根据用户的性别，我们需要限制最大年龄，这意味着我们需要动态更改验证。

首先，我们需要知道“性别”控制的价值何时会改变。这就是为什么我们订阅“valueChanges”事件，在方法内部，我们首先清除所有现有的验证器，然后根据性别添加新的验证器，我们需要手动运行新的验证。

![](img/5180ec91d6cc78f42b0051a0baf2c71a.png)

Validators

另外，让我们为每个性别创建一个额外的问题。男性会问“你的职业是什么？”对女性来说，这将是“你有宠物吗？”。

我们需要在 html 代码中添加这两个问题，并编写两个 ngIf 来检查用户的性别。

![](img/ea7cc30e5e57939835242141203ee562.png)

New questions

接下来，我们需要在 FormGroup 中添加新的 FormControl，并需要验证器。根据用户的“性别”,我们需要删除不必要的 FormControl 并添加新的。

![](img/59341eaf8930175e1f760d7dded020ae.png)

Dynamically change FormGroup

现在我们有了带有动态验证的动态表单。

![](img/86da5b5e6c9778ee43de4b21f56d8c29.png)![](img/6c943b6a90721786102fa8c09b975adc.png)

如果你需要仔细查看项目[，这里有](https://github.com/8Tesla8/tree-view-angular)[链接](https://github.com/8Tesla8/tree-view-angular)。

*原载于 2019 年 6 月 29 日*[*http://tomorrowmeannever.wordpress.com*](https://tomorrowmeannever.wordpress.com/2019/06/29/dynamical-angular-forms/)*。*