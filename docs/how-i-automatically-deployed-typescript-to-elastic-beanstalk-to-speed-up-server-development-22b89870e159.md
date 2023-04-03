# 我如何将 Typescript 自动部署到弹性豆茎，以加快服务器端开发

> 原文：<https://medium.com/quick-code/how-i-automatically-deployed-typescript-to-elastic-beanstalk-to-speed-up-server-development-22b89870e159?source=collection_archive---------0----------------------->

## 通过将 Typescript 代码自动部署到 EB，加快您的开发过程！

![](img/c5573ad2c7bad98e556863ef427ff4b9.png)

A demo of **Booktogether**, an online publishing platform that I created the back-end for.

在我最近的团队项目[book together](https://booktogether.org/)——一个用韩语进行图书管理和评论的在线出版平台——中，我负责使用 Typescript 和 Express 进行服务器端开发，以及使用 AWS 弹性豆茎进行部署。

正如我很快发现的，手动部署到 EB 是一个乏味的过程。我一直在将 Typescript 代码编译成 Javascript，将所有文件压缩成一个. zip 文件，并将压缩文件上传到 AWS。

这时我想，“有没有什么方法可以加快部署速度，让我可以专注于编写工作代码？”在网上做了一些挖掘后，我发现了亚伦·怀特的这个[穿越](https://www.youtube.com/watch?v=VO3tGUFQRKw)帮助我建立了一个 AWS 代码管道，将我的代码直接从 Github 发送到 EB。

## 旁注:常见错误

虽然我在本文中不会深入讨论代码管道的配置细节，但我想说明我在设置这个自动部署系统时遇到的一个问题。当我最初将服务器端代码(以及一个[构建规范文件](https://docs.aws.amazon.com/codebuild/latest/userguide/sample-elastic-beanstalk.html#sample-elastic-beanstalk-codepipeline))从 Github 推送到 AWS 时，EB 控制台出现了以下错误:

> [实例:i-12345]命令在实例上失败。返回代码:1 输出:(截断)…/opt/elasticbeanthal/container files/EB node . py "，第 180 行，npm_install raise e 子过程。CalledProcessError(截断)

正如我后来意识到的，发生这个错误是因为当代码被部署到 EC2 实例时 EB 运行的`npm install`命令。具体来说，一些软件包的安装会触发`node-gyp`过程，该过程由默认的 EC2 用户运行。

默认用户没有权限访问`npm install`创建的 node_modules 目录中的一些包，这就是为什么需要在项目根目录下创建一个名为`.npmrc`的文件并添加以下内容:

```
# Forces node-gyp process to be run as root user, not ec2 default
unsafe-perm=true
```

## 将 Typescript 代码自动部署到 EB

现在回到问题的核心。当代码被部署到弹性豆茎中的 Node.js 环境时，会自动调用`node app`来初始化服务器。然而，因为 Node.js 是一个 Javascript 运行时环境，我为我的服务器编写的 Typescript 代码默认情况下不会运行。我需要首先编译我的 Typescript 代码。

为了在不需要手动将代码上传为. zip 文件的情况下完成这项工作，我有两个选择。第一个是在我的本地机器上使用类似于`npm run build`的命令编译 Typescript 代码，然后将 Javascript 和 Typescript 上传到我的 Github 存储库。然而，这种选择有两个主要缺点:

1.  一个额外的步骤(执行`npm run build`)将被添加到部署流程中
2.  我将不得不应对这两方面的变化。js 和。Github 中的 ts 文件

考虑到这些问题，我求助于另一个选择——特别是使用`.ebextensions`目录。该目录中的文件用于弹性 Beanstalk 环境的高级定制配置。`.ebextensions`文件以 YAML 或 JSON 格式编写，文件扩展名为. config。

我在这个例子中使用的配置文件以这样一种方式配置 EB，即在运行`npm install`之后，实例将所有的类型脚本代码编译成 Javascript。引用下面的[文章](/@lhviet88/deploy-a-typescript-expressjs-into-elasticbeanstalk-nodejs-server-8381e00e7e52)关于使用。ebextensions，我在`.ebextensions`目录中创建了一个名为`source_compile.config`的文件，并包含了以下脚本:

```
# source_compile.config
container_commands:
  compile:
    command: "./node_modules/.bin/tsc -p tsconfig.json"
    env:
      PATH: /opt/elasticbeanstalk/node-install/node-v10.15.3-linuxT-x64/bin/
```

给定配置文件`tsconfig.json`，命令`tsc -p tsconfig.json`告诉 EB 将 Typescript 项目编译成 Javascript。如果在`tsconfig.json`中没有指定`outdir`键，编译后的。js 文件将位于与相应的。ts 文件。

此外，我需要确保在`PATH`键中指定的节点版本与我的 EB 环境使用的节点版本兼容。如果需要，应该更改上面的`source_compile.config`脚本的`PATH`键中使用的节点版本(当前写为 v10.15.3)和操作系统名称。

完成所有这些步骤后，我再次将更改合并到我的主 Github 分支中——瞧！服务器启动并运行。

![](img/788ade7f6694c1c5106c47286bff2520.png)

Health shown as “Ok” after deploying a project with configuration files (buildspec.yml, .npmrc, .ebextensions)

因此，通过利用自动部署管道和`.ebextensions`，我让部署自行处理，并且能够更专注于用 Typescript 编写服务器端代码。对于广泛使用的产品或应用程序，明智的做法是建立一个独立的开发服务器，连接到这个管道和额外的 CI/CD 工具进行严格的测试。