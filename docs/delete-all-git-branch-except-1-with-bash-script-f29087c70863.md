# 用 Bash 脚本删除除 1 以外的所有 Git 分支

> 原文：<https://medium.easyread.co/delete-all-git-branch-except-1-with-bash-script-f29087c70863?source=collection_archive---------5----------------------->

当在一个团队中为一个项目编码时，拥有 Git 存储库的多个分支是不可避免的，并且以后它会使我们的工作空间被我们不再使用的分支所膨胀。

![](img/665340e060a1c7ac6f666a7645a4b168.png)

Photo by [Thought Catalog](https://unsplash.com/@thoughtcatalog?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在我们的机器中删除一个本地 Git 分支非常简单:

```
**git branch -D <branch_name>**
```

但是如果我们有超过 10 个分支(这是非常可能的),重复这个命令是一个糟糕的选择并且花费时间。然后我在网上搜索 git 命令如何删除除了 *master 以外的所有分支。*

***为什么大师分支？*** 因为 Git 中默认的根分支是 master，而且很多程序员在部署应用之前都是用里面的代码作为基础代码。这是我的发现:

```
**git branch | grep -ve " master$" | xargs git branch -D**
```

该命令由产生多个结果的多个命令组成，有:

1.  列出所有前缀为`**master**`的分支名称
2.  获取所有分支名称，并将其作为参数传递给
    `**git branch -D**`命令。

我尝试了这个命令，它运行得很完美，但是有一个问题。这是分支机构的名称。我认为我们作为程序员有时需要删除所有现有的分支，包括主分支，但不包括另一个名称。

所以我决定创建一个简单的 bash 脚本来运行一个 git 命令，它可以删除除 1 分支以外的所有分支。

这个脚本很简单，它只接受一个参数来替换 git 命令中的分支名称。

以下是脚本:

要运行该脚本，您只需键入:

```
**./script.sh <branch_name>**
```

**简单对吧？**

如果你想查看完整的脚本，有一个`**help**`页面，你可以从我的回购中看到它:

[](https://github.com/ridhoperdana/brove) [## ridhoperdana/brove

### 简单的 Git 分支移除。在 GitHub 上创建帐户，为 ridhoperdana/brove 开发做出贡献。

github.com](https://github.com/ridhoperdana/brove) 

祝您愉快！🖖