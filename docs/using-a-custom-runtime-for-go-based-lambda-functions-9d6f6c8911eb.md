# 为基于 Go 的 Lambda 函数使用自定义运行时

> 原文：<https://medium.com/capital-one-tech/using-a-custom-runtime-for-go-based-lambda-functions-9d6f6c8911eb?source=collection_archive---------1----------------------->

## 尽管 Go 在 AWS Lambda 中有原生支持，但切换到自定义运行时也有其优势

![](img/aeebe0f25af085fb393e08c1a5c731a7.png)

Go 是 AWS Lambda 支持的语言，所以你可能会觉得这篇教程的标题有点矛盾。毕竟，你为什么想要使用一个传统的特性来运行你的基于 Go 的函数不支持的语言呢？事实证明，与其他本地支持的语言相比，Lambda 中的 Go 支持有一些差距，可以通过使用自定义运行时来弥补。

然而，我们正在超越自己。在我们深入之前，我们需要讨论 Lambda 支持语言的机制，以及本机支持如何寻找 Go。

# Lambda 运行时:本机与自定义

[运行时](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html)是在你创建 Lambda 函数时呈现给你的一个选项。它定义了 Lambda 函数将如何调用您的处理程序。就本文的讨论而言，它们分为两类——***本土和习俗。***

*   **本地运行时** —定义调用给定语言的处理函数所需的一切。例如，Python 的本机[运行时包括用于运行处理程序代码的 Python 解释器。对于支持本地运行时的语言来说，使用 Lambda 就像打包并上传代码一样简单。](https://docs.aws.amazon.com/lambda/latest/dg/lambda-python.html)
*   **自定义运行时** —要求用户提供调用处理程序代码所需的逻辑。这个逻辑必须作为名为 *bootstrap* 的可执行文件包含在您的 Lambda 代码包中。在 Lambda 函数的初始化过程中，会调用 bootstrap 来执行所提供的处理程序的所有初始化逻辑。这样做之后，它与 Lambda 运行时 API 交互，以获取事件并转发给你的处理程序。自定义运行时是向不提供本机运行时的语言添加支持的一种方式。Rust 是当前必须在自定义运行时中运行的语言的一个例子。

# Go 的本地运行时

AWS 为 Go 提供了一个名为 go1.x 的[原生运行时。与其他本机运行时相比，这个本机运行时是独一无二的，因为它要求您用 AWS 提供的库来包装处理程序代码。](https://docs.aws.amazon.com/lambda/latest/dg/lambda-golang.html)

```
package mainimport (
        "fmt"
        "context"
        "github.com/aws/aws-lambda-go/lambda"
)type Event struct {
        Name string `json:"name"`
}func main() {
        h := func HandleRequest(ctx context.Context, e Event) (string, error) {
            return fmt.Sprintf("Invoked by: %s", e.Name ), nil
        }
        lambda.Start(h)
}
```

`lambda.Start`函数获取您的处理程序并将其包装在一个`net/rpc`服务器中。在冷启动期间，该服务器将其自身绑定到本地套接字，并侦听运行时内置的程序向其发送事件。

# 为 Go 使用定制运行时的三个优点

鉴于 AWS 已经为基于 Go 的 Lambdas 提供了原生运行时，您可能想知道为什么要使用自定义运行时。在我看来，构建自定义运行时有三个优点:

## 1.支持 Lambda 扩展

[Lambda 扩展](https://aws.amazon.com/blogs/compute/introducing-aws-lambda-extensions-in-preview/)是一种在处理程序代码之外提供扩展功能的方式。一个例子是 [Hashicorp 的 Vault 扩展](https://github.com/hashicorp/vault-lambda-extension)，它允许你获取存储在 Vault 中的秘密，并将它们添加到你的 Lambda 函数的环境中。扩展的主要好处之一是它们可以用一种语言编写，并且可以在所有运行时之间共享。

Go 的本地运行时是唯一不支持 Lambda 扩展的运行时。假设扩展的目标是*写一次，到处使用*，这种支持的缺乏意味着必须使用本地运行时为 Lambda 函数开发变通解决方案。如果你想为你的通用 Lambda 任务使用 Lambda 扩展，为 go 使用一个定制的运行时将提供一个跨所有语言运行时的统一体验。

## 2.提供 Amazon Linux 2 执行环境

AWS 正在转移较新的 Lambda 运行时，以使用 [Amazon Linux 2 (AL2)执行环境](https://docs.aws.amazon.com/lambda/latest/dg/lambda-runtimes.html)。除了 Go 之外，所有具有本地运行时的语言都有一个基于 AL2 的运行时。升级到 AL2 是确保您拥有最新执行环境的关键，因为 [Amazon Linux (AL1)已经过渡到生命周期结束支持](https://aws.amazon.com/blogs/aws/update-on-amazon-linux-ami-end-of-life/)。

那么围棋的计划是什么？根据 [AL2 迁移指南](https://aws.amazon.com/blogs/compute/migrating-aws-lambda-functions-to-al2/)，AWS 还没有计划更新原生运行时，并建议切换到名为`provided.al2`的自定义运行时的 AL2 版本。

## 3.统一运行时和处理程序代码

如前所述，Go 的本地运行时分为两个程序，它们通过 RPC 调用进行通信。自定义运行时提供了一个优势，因为您的处理程序代码被编译成与运行时代码相同的二进制文件。好处是所有的 RPC 逻辑都被删除了，可以直接调用处理程序代码。这有助于减少将事件从运行时传输到本机运行时的处理程序所需的多次编码/解码，从而缩短执行时间。

此外，用于 RPC [的库被 Go 团队](https://pkg.go.dev/net/rpc)置于代码冻结状态，不接受新特性或错误修复。在生产代码中消除对这个包的依赖是值得考虑的。

# 从 Go native 运行时迁移到自定义运行时

如果上面提到的任何优点听起来很吸引人，那么本节将帮助您从本机运行时迁移到自定义运行时。

## 变化#1 —构建引导二进制文件

为了让您的处理程序代码在自定义运行时中运行，您需要将它与处理 Lambda 运行时 API 的代码打包成一个名为 bootstrap 的二进制文件。您已经在使用的 aws-lambda-go 库提供了现成的自定义运行时模式。该库有逻辑来确定它是运行在本机运行时还是自定义运行时。这意味着您需要做的只是更改您的构建过程，将生成的二进制文件重命名为 bootstrap，它将在自定义运行时工作，无需对处理程序代码进行任何更改。

示例:

```
GOOS=linux go build -o ./bootstrap ./...
```

## 变化#2 —移除 RPC 以实现更快的冷启动

aws-lambda-go 库提供了一个名为`lambda.norpc`的构建标签，用于从构建的二进制文件中移除所有 RPC 逻辑，从而减小其大小。二进制大小有助于 Lambda 冷启动时间，因此我们所做的任何减少都将有助于改善这一指标。

示例:

```
GOOS=linux go build -o ./bootstrap ./... -tags lambda.norpc
```

## 变更#3 —更新 Lambda 配置

最后一个变化是更新 Lambda 配置中的运行时和处理程序属性，以便在自定义运行时中使用新的引导二进制文件。对于运行时，您将从 go1.x 切换到`provided.al2`。`provided.al2`设置表明我们将“提供”定制的运行时代码来运行在 Amazon Linux 2 执行环境中。对于处理程序，从当前值切换到 bootstrap。

示例(云形成):

```
Resources:
  Function:
    Type: AWS::Serverless::Function
    Properties:
      Handler: bootstrap
      Runtime: provided.al2
      ... # Other required properties
```

# 把所有的放在一起

有了这三个变化，您应该能够利用定制运行时给基于 Go 的 Lambdas 带来的所有优势。所以下次你写一个基于 Go 的 Lambda 时，考虑使用一个定制的运行时！

*披露声明:2022 资本一。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。*