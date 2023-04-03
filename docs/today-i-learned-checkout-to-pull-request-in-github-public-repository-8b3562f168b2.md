# 今天我学习了——在 GitHub 公共存储库中签出请求

> 原文：<https://medium.easyread.co/today-i-learned-checkout-to-pull-request-in-github-public-repository-8b3562f168b2?source=collection_archive---------5----------------------->

## 如何在 GitHub 的公共存储库中检出传入的 pull 请求。

![](img/a8668f25698b98ce13e36ce2ebd4bbbc.png)

[Designed by Rawpixel.com](https://www.freepik.com/free-vector/illustration-of-jigsaw-icon_2606569.htm)

所以今天我在 Github 和开源世界学到了一件真正重要的事情:d .从几个月前开始，我在这里做了一个简单的库:[https://github.com/bxcodec/faker](https://github.com/bxcodec/faker)。这个库帮助 Go 工程师基于他们的结构创建一个虚拟数据。

比如说:

在 Go 中，假设我有如下结构:

```
type Sample struct {
 Name string 
 Age int
}
```

用我的 faker 库，我可以根据它的结构生成一个虚拟数据。

```
sample:= Sample{}
faker.FakeData(&sample)
fmt.Printf("%+v", sample)
//will print: {Name: "Some-random-string", Age: 17 /*random num*/}
```

这个库对于测试(单元测试或者任何需要虚拟数据的东西)非常有用。

几天前，有人创建了一个对我的库的拉请求。通常，如果公关看起来不错，我总是试图把它拉到我的本地，并直接测试它。为此，通常，我只是克隆他的分叉库，然后在我的本地测试它

但是这一次，有点不一样。他在分叉回购中创建了一个分支，然后创建了一个指向我的存储库的直接 PR。所以，如果我克隆他的分叉回购，这个分支就不存在了。对于我的存储库也是一样，如果我获取所有的分支，他的 PR 分支就不存在。

那么如何结账去公关分公司呢？

## 在 Github 中检出拉请求

为此，我们只需要更改我们的`.git`文件夹中的`config`文件。对于我的案例，在我的项目库中会有一个名为`.git`的隐藏文件夹

```
$ pwd
/Users/iman/go/src/github.com/bxcodec/faker
$ ls -lah
drwxr-xr-x  33 iman  staff   1.0K Nov 22 11:36 .
drwxr-xr-x   7 iman  staff   224B Nov 12 11:33 .. 
drwxr-xr-x  15 iman  staff   480B Nov 22 11:36 .git 
...
```

在。`git`文件夹中会有一个名为`config`的文件。它看起来更像这样。

```
$ cat .git/config
[core]
        repositoryformatversion = 0
        filemode = true
        bare = false
        logallrefupdates = true
        ignorecase = true
        precomposeunicode = true
[remote "origin"]
        url = [https://github.com/bxcodec/faker](https://github.com/bxcodec/faker)
        fetch = +refs/heads/*:refs/remotes/origin/* 
[branch "master"]
        remote = origin
        merge = refs/heads/master
```

因此，为了使我们能够结帐到任何传入的 PR，我们需要将
`fetch = +refs/pull/*/head:refs/remotes/origin/pr/*`添加到 remote origin 部分。

之前:

```
[remote "origin"]
        url = [https://github.com/bxcodec/faker](https://github.com/bxcodec/faker)
        fetch = +refs/heads/*:refs/remotes/origin/*
```

之后:

```
[remote "origin"]
        url = [https://github.com/bxcodec/faker](https://github.com/bxcodec/faker)
        fetch = +refs/heads/*:refs/remotes/origin/*
        **fetch = +refs/pull/*/head:refs/remotes/origin/pr/***
```

这样做之后，我们可以将所有传入的拉请求提取到我们的存储库中。

```
$ git fetch origin
From github.com:bxcodec/faker
 * [new ref]         refs/pull/10/head -> origin/pr/10
 * [new ref]         refs/pull/11/head -> origin/pr/11
 * [new ref]         refs/pull/12/head -> origin/pr/12
 * [new ref]         refs/pull/13/head -> origin/pr/13
 ...
```

一会儿，我们去公关部结账吧

```
$ git checkout pr/10 
```

无论如何，这在 Gitlab 中也是可行的，也许对其他 Git 主机也是可行的。

即使有点不同，但步骤是相似的。

## 在 Gitlab 中检出合并请求(Pull 请求)

步骤和 Github 中的一样，区别只是术语和关键字。在 Gitlab 中，他们称之为合并请求，而不是拉请求。因此，由于这个不同的术语，一些网址也将不同。

因此，对于 Gitlab 案例，我们需要在同一个部分添加这一行。

```
fetch = +refs/**merge-requests**/*/head:refs/remotes/origin/**merge-requests**/*
```

那就去拿。

```
$ git fetch origin
```

稍后签出到合并请求编号。

```
$ git checkout merge-requests/10
```

参考资料:

*   [https://gist.github.com/piscisaureus/3342247](https://gist.github.com/piscisaureus/3342247)
*   [https://docs . git lab . com/ee/user/project/merge _ requests/# check out-locally-by-modificing-git config-for-a-given-repository](https://docs.gitlab.com/ee/user/project/merge_requests/#checkout-locally-by-modifying-gitconfig-for-a-given-repository)