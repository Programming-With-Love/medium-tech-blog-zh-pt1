# 今天我学习了:从您的远程分支机构结帐

> 原文：<https://medium.easyread.co/today-i-learn-checkout-from-your-remote-branch-afe3193b3788?source=collection_archive---------2----------------------->

![](img/4672b8eaeed77c2cc0d68befd5e11fd2.png)

Stackify copyrighted

## 好吧，这不是一个古怪的建议，但它确实有效

```
git checkout --track [remote]/[branch]
```

有了这个，你就不必手动从一个本地分支机构结账，然后做一个 ***git 拉*** 的方式。上面的命令将节省我们大量的时间来直接弄脏我们的手。

您可能还会奇怪，这个命令的目的与下面的不一样吗

```
git checkout -b [mybranch] [remote]/[branch]
```

嗯，答案是肯定的，但是这个命令是为不同的情况设计的。

1.  ***- track*** 单独从远程直接匹配分支名称和头
2.  ***-b【my branch】***相同的目的，但几乎没有额外的功能来定制或重命名我们的分支。

那么，你是哪一边的？黑暗面还是光明面？明智地选择你的立场。

也许现在就这样吧。你可以在下面评论任何其他有趣的案例来纠正我的错误或者在这篇文章中给出任何反馈。

~谢谢~

参考资料:

[](https://www.git-tower.com/learn/git/faq/checkout-remote-branch) [## git 签出远程分支

### 我们的学习部分帮助你开始学习各种网络和软件技能。免费在线书籍、视频和电子书获得…

www.git-tower.com](https://www.git-tower.com/learn/git/faq/checkout-remote-branch) [](https://stackoverflow.com/a/10002469/3763032) [## git 检验-追踪起点/分支和 git 检验-b 分支起点/分支之间的区别

### 感谢贡献一个堆栈溢出的答案！请务必回答问题。提供详细信息并分享…

stackoverflow.com](https://stackoverflow.com/a/10002469/3763032)