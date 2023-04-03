# 今天我学到了——修复:获取私有存储库返回错误终端提示被禁用

> 原文：<https://medium.easyread.co/today-i-learned-fix-go-get-private-repository-return-error-terminal-prompts-disabled-8c5549d89045?source=collection_archive---------0----------------------->

## 修复致命错误:无法读取“https://githost[的用户名。]something ':终端提示被禁用

![](img/9231fcc9d5a7315494ca37e44e9ad086.png)

我将一些项目分成 3 个 git 主机提供者:Github、Gitlab 和 Bitbucket。因为我是一个低调的软件工程师，所以我在 Gitlab 和 Bitbucket 中使用私有库，在 Github 中使用公共库 XD。

我非常喜欢 Github，但是我目前的经济状况迫使我减少任何*不那么重要的*成本，仅仅是为了我的个人爱好和私人项目 XD。

所以，几天前，我正在尝试创建一个 Golang 的项目，并将其存储在 Gitlab 中。这个项目对我的另一个库有 2 个依赖。其中一个存储在 Bitbucket，另一个在 Gitlab。

为了简化，我举个例子来讲。假设我有 3 个项目。

*   `math`bitbucket 中的库项目(私有存储库)
*   `multiply`git lab 中的库项目(私有库)
*   `samplecool`git lab 中的项目(私有存储库)

因此，`samplecool`项目将`math`和`multiply`作为其依赖项，也就是说，`math`和`multiply`被导入到`samplecool`项目中。

```
package samplecoolimport(
  "bitbucket.org/bxcodec/math"
  "gitlab.com/bxcodec/multiply"
)
```

而当我试图`go get`对`samplecool`的依赖时，这个错误发生了:

```
$ go get -v
# cd .; git ls-remote [https://bitbucket.org/bxcodec/math](https://bitbucket.org/bxcodec/math)
**fatal: could not read Username for '**[**https://bitbucket.org'**](https://bitbucket.org')**: terminal prompts disabled**
go: missing Mercurial command. See [https://golang.org/s/gogetcmd](https://golang.org/s/gogetcmd)
package bitbucket.org/bxcodec/math: [https://api.bitbucket.org/2.0/repositories/bxcodec/math?fields=scm](https://api.bitbucket.org/2.0/repositories/bxcodec/math?fields=scm): 403 Forbidden
Fetching [https://gitlab.com/bxcodec/multiply?go-get=1](https://gitlab.com/bxcodec/multiply?go-get=1)
Parsing meta tags from [https://gitlab.com/bxcodec/multiply?go-get=1](https://gitlab.com/bxcodec/multiply?go-get=1) (status code 200)
get "gitlab.com/bxcodec/multiply": found meta tag get.metaImport{Prefix:"gitlab.com/bxcodec/multiply", VCS:"git", RepoRoot:"[https://gitlab.com/bxcodec/multiply.git](https://gitlab.com/bxcodec/multiply.git)"} at [https://gitlab.com/bxcodec/multiply?go-get=1](https://gitlab.com/bxcodec/multiply?go-get=1)
gitlab.com/bxcodec/multiply (download)
# cd .; git clone [https://gitlab.com/bxcodec/multiply.git](https://gitlab.com/bxcodec/multiply.git) /Users/imantumorang/go/src/gitlab.com/bxcodec/multiply
Cloning into '/Users/imantumorang/go/src/gitlab.com/bxcodec/multiply'...
**fatal: could not read Username for '**[**https://gitlab.com'**](https://gitlab.com')**: terminal prompts disabled**
package gitlab.com/bxcodec/multiply: exit status 128
```

如果我们寻找更多的细节，我发现的错误是:

```
**fatal: could not read Username for '**[***githost.something*'**](https://gitlab.com')**: terminal prompts disabled**
```

我看到这里发生了类似的错误。在互联网上找到解决方案后，我找到了一些帮助我解决这个问题的命令。

## 修复致命错误:无法读取 Gitlab 的用户名:终端提示被禁用

对于 Gitlab 的情况，这个命令应该可以修复这个错误(*将这个复制到终端)*

```
$ git config --global url."git@gitlab.com:".insteadOf "https://gitlab.com/"
```

这将使用我们的`/.ssh`，所以确保我们的 ssh 密钥存在并在我们的 Gitlab 中注册。

为了确保该命令有效，当我们在`.gitconfig`中看到,`.gitconfig`中将会增加类似这样的内容:

```
$ cat ~/.gitconfig
[url "[git@gitlab.com](mailto:git@gitlab.com):"]
 insteadOf = [https://gitlab.com/](https://gitlab.com/)
```

## 修复致命错误:无法读取位存储桶的用户名:终端提示被禁用

bitbucket 也是如此。

```
$ git config --global url."[git@bitbucket.org](mailto:git@bitbucket.org):".insteadOf "https://bitbucket.org/"
```

而我的`.gitconfig`会变成下面这样:

```
$ cat ~/.gitconfig
[url "[git@gitlab.com](mailto:git@gitlab.com):"]
 insteadOf = [https://gitlab.com/](https://gitlab.com/)
[url "[git@bitbucket.org](mailto:git@bitbucket.org):"]
 insteadOf = [https://bitbucket.org/](https://bitbucket.org/)
```

在 Gitlab 和 Bitbucket 中完成这些之后，现在我可以`go get`所有的依赖项了。

这也适用于 Github。如果你尝试 GitHub，只需更改 URL 目标和 URL 来源。

```
$ git config --global url."[git@github.c](mailto:git@bitbucket.org)om:".insteadOf "https://github.com/"
```

参考资料:

*   [https://bykov . tech/2016/05/31/using-standard-command-go-get-for-private-golang-packages-on-git lab-com/](https://bykov.tech/2016/05/31/using-standard-command-go-get-for-private-golang-packages-on-gitlab-com/)