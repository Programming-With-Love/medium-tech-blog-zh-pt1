# 如何将现有项目添加到 git

> 原文：<https://medium.com/bb-tutorials-and-thoughts/how-to-add-existing-project-to-git-1577789c2a27?source=collection_archive---------2----------------------->

![](img/8cd640b63288eda76f0f4cd3f7c95e76.png)

以下是用于初始设置 repo 和将代码添加到远程存储库的命令列表

```
git initgit add .git commit -m "your initial commit"git remote add origin <remote repo url>git push -u origin master
```

## 疑难解答问题

有时您可能会收到这样的消息:本地分支与跟踪分支消息不同步。您应该运行此命令来重设基础。

```
git pull --rebase
```

感谢您的阅读！！