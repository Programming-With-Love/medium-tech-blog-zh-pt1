# 在 Golang 1.18 上修复 ReadBuildInfo 中的空 main

> 原文：<https://medium.easyread.co/fixing-empty-main-from-readbuildinfo-on-golang-1-18-ed8d1d671c5d?source=collection_archive---------3----------------------->

![](img/745a7c3fbac73cae9a88ca995c261de7.png)

Image by wikipedia.org

大家好！再次与我分享一些经验，尽管或者关于软件工程领域相关技术的看法。今天，我将分享我在 Golang 版本上从 ReadBuildInfo()读取一些数据时解决一个问题的经验。

那么，问题是什么？先说我为什么用这个功能的背景。情况是我想读取一些项目的模块名。

带有 go 模块名`example/readbuildinfo`的代码如下所示:

```
**package** main

**import** (
   **"encoding/json"
   "log"
   "runtime/debug"** )

**func** main() {
   info, ok := debug.ReadBuildInfo()
   **if** !ok {
      log.Fatalf(**"failed to get build info\n"**)
   }

   b, err := json.MarshalIndent(info, **""**, **"\t"**)
   **if** err != nil {
      log.Fatalf(**"failed to marshall: %s\n"**, err)
   }

   log.Println(string(b))
}
```

如果我们运行`go run main.go`，那么它将返回:

```
{
 "Path": "command-line-arguments",
 "Main": {
  "Path": "example/readbuildinfo",
  "Version": "(devel)",
  "Sum": "",
  "Replace": null
 },
 "Deps": null
}
```

是的，在 Main 下，我们可以在 Path 字段中获得模块名，所以没有问题！

但是，在对另一个项目做了同样的事情后，它并不顺利。查了下有什么区别，貌似是因为 Golang 版本的原因。早期的项目用的是 1.16 版本，进展不顺利的项目用的是 1.18。这是使用 1.18 的项目的输出:

```
{
 "GoVersion": "go1.18.5",
 "Path": "command-line-arguments",
 "Main": {
  "Path": "",
  "Version": "",
  "Sum": "",
  "Replace": null
 },
 "Deps": null,
 "Settings": [{
   "Key": "-compiler",
   "Value": "gc"
  },
  {
   "Key": "CGO_ENABLED",
   "Value": "1"
  },
  {
   "Key": "CGO_CFLAGS",
   "Value": ""
  },
  {
   "Key": "CGO_CPPFLAGS",
   "Value": ""
  },
  {
   "Key": "CGO_CXXFLAGS",
   "Value": ""
  },
  {
   "Key": "CGO_LDFLAGS",
   "Value": ""
  },
  {
   "Key": "GOARCH",
   "Value": "arm64"
  },
  {
   "Key": "GOOS",
   "Value": "linux"
  }
 ]
}
```

有点跑题了；为了方便地切换 Go 版本，我们可以使用 Docker 来运行这个简单的脚本。以下是 Dockerfile 文件的示例:

```
**FROM** golang:1.18

**WORKDIR /**go**/**src**/**app
**ADD** . **/**go**/**src**/**app

**CMD** [**"go"**,**"run"**,**"main.go"**]
```

这是构建它的命令:

```
docker build -t readbuildinfo .
```

对于运行镜像可以使用这个命令:

```
docker run readbuildinfo
```

回到主话题:然后在寻找解决方案后发现了这个问题[https://github . com/golang/go/issues/51831 # issue comment-1074188363](https://github.com/golang/go/issues/51831#issuecomment-1074188363)

接下来，当使用特定文件和仅使用点(.)

将运行命令从:

```
go run main.go
```

收件人:

```
go run .
```

然后它显示:

```
{
 "GoVersion": "go1.18.5",
 "Path": "example/readbuildinfo",
 "Main": {
  "Path": "example/readbuildinfo",
  "Version": "(devel)",
  "Sum": "",
  "Replace": null
 },
 "Deps": null,
 "Settings": [{
   "Key": "-compiler",
   "Value": "gc"
  },
  {
   "Key": "CGO_ENABLED",
   "Value": "1"
  },
  {
   "Key": "CGO_CFLAGS",
   "Value": ""
  },
  {
   "Key": "CGO_CPPFLAGS",
   "Value": ""
  },
  {
   "Key": "CGO_CXXFLAGS",
   "Value": ""
  },
  {
   "Key": "CGO_LDFLAGS",
   "Value": ""
  },
  {
   "Key": "GOARCH",
   "Value": "arm64"
  },
  {
   "Key": "GOOS",
   "Value": "linux"
  }
 ]
}
```

耶！它起作用了！！对于 docker 文件，只需更改这一行:

```
**CMD** [**"go"**,**"run"**,**"main.go"**]
```

到

```
**CMD** [**"go"**,**"run"**,**"."**]
```

> 实际上我也试着检查了 1.19 版本，但是 1.19 的行为在 1.18 版本中保持不变。

希望你喜欢它，如果这篇文章对你有用，我很高兴！

谢谢大家！