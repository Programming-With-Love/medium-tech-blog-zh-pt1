# 带有 GraalVM 和 TensorFlow.js 的 Finder bot

> 原文：<https://medium.com/oracledevs/finder-bot-with-graalvm-and-tensorflow-js-29710c0d21e3?source=collection_archive---------0----------------------->

![](img/a19e9ff92416a8da6a7edded3ff2fbb7.png)

Photo by [Joseph Chan](https://unsplash.com/photos/C8VWyZhcIIU?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/bot?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 介绍

在处理文本搜索和文本处理任务时，grep、awk 和 sed 等实用程序是非常强大的命令行工具。话虽如此，我很少使用它们，因为大多数时候我最喜欢的 IDE 已经为我完成了工作。有时，我会在 google 上搜索 grep、awk 和 sed 命令或它们各自的手册页，以获得正确的命令和选项。我一直在想，我们是否可以利用在 [NLP](https://en.wikipedia.org/wiki/Natural_language_processing) 和 [NLU](https://en.wikipedia.org/wiki/Natural_language_understanding) 中取得的惊人进步，并添加一些机器学习，将这些命令隐藏在命令行机器人(CLB)后面，并通过自然文本处理常见用例。

比如说“给我找出 buildpal/ui 下所有包含‘Mady’这个词的 JavaScript 文件”就不错了。这相当于执行以下命令:

```
grep --include=\*.js -rnwl buildpal/ui -e "Mady"
```

当然，使用工具是个人选择的问题。上面的命令非常简洁，一旦所有选项都正确，它就能很好地完成工作。

让我们来谈谈构建 finder 命令行 bot 的一些高级方面。CLB 应该采用简单的英语表达，如“在 foo/bar 下查找包含‘world’的文件”，将其转换为命令，执行并返回结果。我们将看看 GraalVM 和 Tensorflow.js 如何潜在地用于这个实验。

# 意图分类和实体提取

与三年前相比，ML 和 NLU 的当前状态使得意图分类和实体提取变得相对容易。简单来说，意图是用户试图完成的事情，或者是用户查询或陈述背后的意图。在我们的 finder CLB 领域中，一个意图是“查找”或“搜索”文件。我们训练好的模型的工作是将给定的话语分类成这些特定意图中的一个。

实体提取是识别携带与意图相关的附加信息的关键字的过程。例如，在话语“在 buildpal/ui 下为我查找包含‘Mady’的所有 JavaScript 文件”中，实体可以是文件夹路径“buildpal/ui”、文件类型“JavaScript”和搜索词“Mady”。可以使用正则表达式或通过更复杂的技术(如词性标注或命名实体识别)来提取实体。在开始之前，我们需要对意图进行分类，并训练我们的模型。在后续的文章中，我将介绍模型的设置、训练和验证。同时，请参考这篇伟大的[文章](/oracledevs/text-classification-with-deep-neural-network-in-tensorflow-simple-explanation-be07c6cbe867)。

# GraalVM

甲骨文最近发布了一款多语言虚拟机 [GraalVM](https://www.graalvm.org/) ，它可以执行用 Java、C、Scala、JavaScript 和 Python 等语言编写的程序。我们将看到如何利用它来建立发现者 CLB。第一步是安装 GraalVM 并设置相关的环境变量。已安装的 bin 目录包含 JavaScript 和 Node.js 的附加启动器。如果您已经像我一样安装了 Node.js，您可以使用别名指向 Graal 的版本。

```
alias gnode='/path/to/graal/bin/node'
alias gnpm='/path/to/graal/bin/npm'
```

# TensorFlow.js

如您所知，TensorFlow 框架有多种风格。虽然我们可以使用 TensorFlow for Java API，但是让我们修补一下 JavaScript [版本](https://js.tensorflow.org/)，看看它如何作为节点包与 GraalVM 一起工作。

首先，用“gnpm init”初始化您的项目。接下来，使用以下命令安装所需的 TensorFlow 包:

```
gnpm install @tensorflow/tfjs
npm install @tensorflow/tfjs-node
```

请注意，我无法使用 Graal 的 npm 安装本机 TensorFlow 包。“libtensorflow.so”有问题。因此，作为一种变通方法，我使用了我的系统上可用的常规 npm。

# 发现者机器人

从更高的层面来看，让我们看看如何将不同的部分整合在一起。我们使用 TensorFlow 对象加载训练好的模型:

```
const tf = require('[@tensorflow/tfjs](http://twitter.com/tensorflow/tfjs)');
require('[@tensorflow/tfjs-node](http://twitter.com/tensorflow/tfjs-node)');tf.loadModel('file://clb/model.json').then(model => {...});
```

该模型将用于识别用户的意图。

Java 或 Node.js 都可以用来实现功能的其余部分——从命令行读取用户的句子，使用 NIO 查找文件，或者如果 grep 可用，使用 process builder 执行 grep 命令。为了混合 Java 和 JavaScript，我们必须向 Graal 传递额外的标志:

```
gnode --polyglot --jvm index.js
```

当模型以合理的置信度识别出“查找”意图时(我们可以配置错误阈值)，我们可以继续提取实体。带有正则表达式的简单模式匹配对我们的用例来说很好。完成后，我们继续构建和执行 grep 命令并读取其输出。如果 grep 不可用，我们可以选择使用 Java 或 Node.js 实现查找功能。

# 结论

这是我们短暂而有趣的实验。我们讨论了构建具有有限 NLU 功能的命令行机器人的一些方面，该机器人可以将常规的英语句子转换成可执行的命令。

我将以最后一句话结束。对于 NLU 和服务模型，无论是本地还是远程，肯定有其他成熟的方法——使用您选择的框架和/或通过像 [GraphPipe](https://oracle.github.io/graphpipe) 这样的服务器协议。

在本文的第 2 部分，我们将深入探讨意图分类模型。