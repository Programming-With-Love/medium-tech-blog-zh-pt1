# 利用 Docker-Compose 为您的企业 Jenkins CI 渠道提供超强动力

> 原文：<https://medium.com/capital-one-tech/super-powering-your-enterprise-jenkins-ci-pipeline-with-docker-compose-a4f508598375?source=collection_archive---------0----------------------->

![](img/8cf04f30965790a6692722e8a4b43341.png)

而 Jenkins 可以灵活地添加几乎无限的插件和功能；它是在容器出现之前被创造出来的。最初，它主要通过 UI 进行交互，直到 2016 年才使代码优先管道成为规范；此举恰逢管道*(之前的工作流)* [插件推出测试版](https://www.cloudbees.com/blog/beta-release-github-support-pipeline-code)和 [CloudBees](https://www.cloudbees.com/) 开源他们的[管道可视化插件](https://wiki.jenkins.io/display/JENKINS/Pipeline+Stage+View+Plugin)。

但在 2018 年，集装箱是常态。为了帮助我在 Capital One 的团队，我想在 Jenkins 环境的每个构建步骤中使用容器。下面是一个具体的修复方法，可以使 Jenkinsfile 更具可读性，并使使用 Docker 映像进行构建的步骤更简单。

# image.inside()有问题

“image.inside()”函数为在 Docker 容器中运行命令提供了很好的便利。使用它看起来像这样:

```
node { checkout scm docker.withRegistry('https://registry.example.com') { docker.image('my-custom-image').inside { sh 'make test' } }}
```

但是，对于您的项目，您可能不希望以 root 身份运行 Jenkins 构建代理，而是以另一个用户的身份运行。对于某些安全情况来说，将它们分开可能是有意义的，但是 Docker Jenkins 插件仍然期望某种形式的特权访问。正如我们所发现的，以这种方式运行 Jenkins 构建代理会导致***“image . inside()”函数不工作并抛出异常。***

虽然有一个变通办法，但它使代码更难阅读。在下面的语法正确但不起作用的*(这实际上不是我们的代码)* groovy 代码示例中，在没有“img.inside()”函数工作的情况下，当在容器中运行 bash 中的命令时，我们必须将它们连接起来。不是可读性最强的代码；以及可能很快失控的解决方案。

```
node{ docker.withRegistry(‘https://registry.example.com/', ‘svc-acct’) { def img = docker.image(‘org/go-builder-base-image:master’) img.pull() checkout scm stage(‘Build’) { sh ‘docker run — privileged -t -v $(pwd):/go/src/git.example.com/group/registrysync -w /go/src/git.example.com/group/registrysync group/go-builder-base-image:master bash -c “go get && go build”’ } stage(‘Test’) { sh ‘docker run — privileged -t -v $(pwd):/go/src/git.example.com/group/registrysync -w /go/src/git.example.com/group/registrysync group/go-builder-base-image:master bash -c “go test”’ } }}
```

当您将这段代码与过去五年发布的 CI 系统进行比较时，您可以为每个步骤指定一个 Docker 映像，这需要开发人员做更多的工作。

***输入 Docker 作曲。***

# 隔离各处的构建步骤

[Docker Compose](https://docs.docker.com/compose/) 是一个工具，用于在比仅使用 docker run 更高的级别上编排一个或多个容器、网络和卷。它在我们的每一个 Jenkins 节点上都可用，在你安装 Docker 的任何地方都可用。如果你没有在 Jenkins 上安装 Docker 插件，我强烈推荐。事实上，它是 [Docker 的编排系统 Swarm](https://docs.docker.com/engine/swarm/) 的一部分，但它不一定要以那种方式使用。

Docker Compose 提供了一种更结构化、更简洁的方式来将变量和值传递给 Docker 命令。与 docker 文件不同，您可以访问环境变量并将它们传递给 docker 命令。Docker 团队在这里对 Docker Compose [有一个很好的概述。](https://docs.docker.com/compose/overview/)

让我们回到当前的需求— *让 Jenkinsfile 更具可读性，让 Docker 映像的构建步骤更简单。*

我们将使用的基本模式是在 Docker 编写文件中为每个构建步骤定义一个服务，并为每个步骤定义一个相关的 Docker 文件。

在这个例子中，我们有一个要编译的 Go 项目。创建一个 docker 文件来完成这项工作非常简单。我们将使用一个安装了 Go 环境的基础映像，我们可以在其中调用“go build”。

```
FROM registry.example.com/group/go-builder-base-image:masterARG PROJECT_PATH
VOLUME $PROJECT_PATH
WORKDIR $PROJECT_PATHCMD CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -ldflags ‘-s’ -x .
```

您可以看到，我们指定了一个 PROJECT_PATH 参数，然后用它来指定卷并设置工作目录。*记住——环境变量在 docker 文件中不可用。*

我们现在可以使用这个 Docker 文件作为构建步骤，在 Docker-Compose 文件中编译我们的 Go 项目。

```
version: '3'
  services:
    compile:
      build:
        context: .
        dockerfile: compile.dockerfile
      args:
        - PROJECT_PATH=$PROJECT_PATH
      volumes:
        - .:$PROJECT_PATH
```

在 Docker Compose 文件中，您**可以**使用环境变量，这正是我们对$PROJECT_PATH 所做的。正如您可能已经猜到的那样，在 Jenkins 构建环境中定义了当前项目的路径。我们在附加卷时使用它来确保我们的项目位于容器中的特定位置。

现在我们有了管道，让我们看看如何使用它。

" docker-compose-f pipeline-compose . yml run-RM compile "

这个命令将告诉 docker-compose 使用 pipeline-compose.yml 文件，并运行编译“服务”和删除任何创建的图像。这比我们最初使用的变通方法更容易阅读。这是为了唤起你的记忆:

" docker run-privileged-t-v $(pwd):/go/src/git . example . com/group/registry sync-w/go/src/git . example . com/group/registry sync group/go-builder-base-image:master go build "

现在，让我们将新的 docker-compose 步骤放到 jenkinsfile 中。这是一种方法，但凭借詹金斯的灵活性，还有其他方法。

```
node{ docker.withRegistry(‘https://registry.example.com/', ‘svc-acct’) {

    checkout scm
    stage(‘Build’) { sh ‘docker-compose –f build-compose.yml run –rm compile’

    } }}
```

如果我们对测试步骤做同样的事情，我们会得到如下结果:

```
node{ docker.withRegistry(‘https://registry.example.com/', ‘svc-acct’) { checkout scm stage(‘Build’) { sh ‘docker-compose –f build-compose.yml run –rm compile’ } stage(‘Test) { sh ‘docker-compose –f build-compose.yml run –rm test’ } }}
```

这个例子很简单，但是它很好地分享了这个概念。

以这种方式使用 Docker Compose 有几个好处:

1)您可以为步骤的依赖关系编排其他容器；例如模拟 API 或 DB。(你已经在做了，对吗？对吗？)

2)您可以在本地机器上执行每一步，而不依赖于正在安装的 Docker。

第二个好处真的很好，不是每个人都想到的。这让团队有信心知道，当他们提交时，他们在本地运行的步骤也将在 CI 环境中工作。

# 结论

并不是每个公司或项目在他们的 Jenkins 环境中都有相同的约束，所以并不是每个人都会在使用“image.inside()”函数时遇到相同的问题。我希望，即使您没有遇到我们遇到的同样的挑战，您仍然会考虑在您的 Jenkins 环境中使用 Docker Compose，因为它对您的管道的好处远远超出了这个特定的解决方法。

*披露声明:这些观点是作者的观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权都是其各自所有者的所有权。本文为 2018 首都一。*