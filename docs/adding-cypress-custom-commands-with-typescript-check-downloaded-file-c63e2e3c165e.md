# 使用 Typescript 添加 Cypress 自定义命令:检查下载的文件

> 原文：<https://medium.com/version-1/adding-cypress-custom-commands-with-typescript-check-downloaded-file-c63e2e3c165e?source=collection_archive---------3----------------------->

![](img/ee51b22743a59d2a9d1d5d1c97883758.png)

# 柏树和打字稿

赛普拉斯船带有[官方型号声明](https://github.com/cypress-io/cypress/tree/develop/cli/types)为[打字稿](https://www.typescriptlang.org/)。这允许您用 TypeScript 编写测试。这非常有用，因为 Typescript 使用强类型，这有助于我们在编译时检测错误，但也意味着我们总是需要定义将要使用的类型。

# Cypress 自定义命令

Cypress 自带 API 来创建自定义命令和覆盖现有命令，这允许我们封装功能，这样我们就不必重复代码，使我们的测试更具可读性。

# 检查下载的文件命令

作为一个例子，让我们假设我们已经定义了几个测试，在所有的测试中，我们想要检查一个下载的文件及其内容。我们可以创建一个命令来简化我们的工作，而不是在所有的测试中添加相同的代码。

第一步是创建一个包含所有命令逻辑的函数。我们的命令应该在 *cypress/support/* 中，所以我们将使用现有的文件 *commands.ts* 来添加我们的命令:

在这个文件中，我们将创建函数 *checkDownloadedFile* (第 13–20 行)，该函数接收文件名并使用默认的 cypress 文件夹构建文件路径:

然后我们将使用 cypress 的 *readFile* 函数打开文件，如果它存在，检查其内容:

最后，您可以将 *checkDownloadedFile* 命令添加到全局 Cypress 可链接接口:

最后一步，我们需要向 cypress 添加新命令，以便能够与 *cy* 对象一起使用。我们需要使用 *cypress/support/中的 *index.ts* 来添加我们的新命令:*

仅此而已！现在我们可以在任何测试中使用它:

# 可链接命令

正如您所看到的，前面的命令是 void 类型的，但是如果我们需要它是可链接的呢？没有问题！

按照上一节的相同方法，我们只需要添加新的命令函数，但是在这种情况下，我们使用我们需要找到的所有内容返回 *cy* 对象，在这种情况下，是一个包含“下载”的按钮:

就像之前一样，我们必须在*cypress/support/index . ts*中添加我们的新命令:

所以现在我们可以在测试中使用它:

正式柏树文件:[柏树和打字稿](https://docs.cypress.io/guides/tooling/typescript-support#Install-TypeScript)

![](img/ae860db159519157d1ce7de0bc471f5f.png)

**关于作者:** *Jaime Torres 是 1 版 NIDCS 实践中的高级软件工程师。*