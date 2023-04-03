# 使用 Git 挂钩改进您的开发工作流程

> 原文：<https://medium.com/google-developer-experts/using-git-hooks-to-improve-your-development-workflow-8f5a1fb81ec7?source=collection_archive---------1----------------------->

![](img/6e2be3d9595b3c0b0df3ad80a5202c2d.png)

最近，我第一次为一个新的代码库做贡献。我扩展并实现了一些我需要的功能。在我的机器上进行彻底的测试后，我检查了功能是否正常工作，我提交了我的贡献。几分钟后，我们的 CI 环境传达了一条信息:

> 4 项测试失败

这种情况经常发生，甚至在我们习惯使用的代码库上也是如此。我们倾向于专注于开发新的特性，而忘记了有一个覆盖它们的测试。或者需要进行新的测试来覆盖新的特性。这一事实本身并不是一个悲剧，但在这种情况下，工作流程肯定可以改进。我们可以使用 Git 挂钩来改善它们。

[Git 挂钩](https://githooks.com/)是在不同的 Git 事件之前或之后执行的脚本。例如:提交、推送和接收。它们是一个内置的解决方案(不需要下载任何第三方插件),它们在你的机器上本地执行。我们可以应用它们的各种场景是相当大的。

让我们考虑前面的场景。现代技术世界倾向于在开发的早期阶段解决问题。例如，整个可空性承诺是关于在编译过程中传递错误，而不是在运行时。这对高质量的产出有着决定性的积极影响。类似地，如果我们可以在我们的机器上测试失败，我们肯定会改进我们的工作流，而不是在 CI 环境中测试失败，以及所有的副作用(在我们的机器上修复测试，测试，重新推送，运行 CI)。

让我们假设我们有一个运行 Gradle 的 Android 应用程序——尽管任何运行在 Gradle 上的应用程序都可以工作。当我们运行测试时，我们基本上是在运行一个类似于下面的命令:

> 。/gradlew 清洁测试

我们希望在实际推送代码之前执行这个命令。为了做到这一点，我们需要做到以下几点:

1.  去看*。存储库的 git/hooks* 文件夹。
2.  创建一个名为*的预推送文件*
3.  复制以下代码片段:

现在，在你推送代码之前的任何时候，测试都将在本地运行。如果有任何错误，将不会有推送(当脚本返回 1 时)。

注意，您可以决定在每次提交时这样做，而不是在每次推送时这样做。如果是这种情况，您将需要在提交前修改文件*。我相信在推送之前运行测试比在提交之前运行测试更有效，但是您可能需要看看这如何适合您的工作流。*

*有一些有趣的想法，我们可以使用 Git 挂钩来自动化一些方面。例如，我们可能想要运行的任务之一是使用诸如 [lint](https://en.wikipedia.org/wiki/Lint_(software)) 或 [detekt](https://github.com/arturbosch/detekt) 之类的工具进行静态分析。对于这个例子，让我们使用前者，并把它存储在一个*预提交*文件中。*

*Full gist: [https://gist.github.com/kikoso/a46e6a2efd07ab66ed049b5f7b76ace5](https://gist.github.com/kikoso/a46e6a2efd07ab66ed049b5f7b76ace5)*

*如果我们想在一个*预推*钩子上一起执行它们，我们实际上可以将它们结合起来:*

*Full gist: [https://gist.github.com/kikoso/0a79740c7cde9174ffbafe19caa39306](https://gist.github.com/kikoso/0a79740c7cde9174ffbafe19caa39306)*

*在任何过程中，都有一些我们需要执行的手动任务，并且很容易忘记。Git 挂钩可以确保(或者至少提醒用户)这些 tak 应该在提交被实际推送之前执行:*

*Full gist: [https://gist.github.com/kikoso/d6024e455c733fb91798621ae487a375](https://gist.github.com/kikoso/d6024e455c733fb91798621ae487a375)*

*有几个想法我们可以应用。例如，根据您的流程，您可能希望在将开发分支合并到主分支之后立即创建一个发布标记。使用 Git 挂钩很容易实现这一点:*

*这实际上可以进一步自动化(代替在控制台上键入标签，你可以从你的 Gradle 文件中读取，或者从你存储它的环境变量中读取。*

*这是可用的 git 挂钩的完整列表。我们不会对它们中的每一个做进一步的阐述，因为它们的名字是不言自明的。*

*   *[**applypatch-msg**](https://github.com/git/git/blob/master/templates/hooks--applypatch-msg.sample)*
*   *[**预申请补丁**](https://github.com/git/git/blob/master/templates/hooks--pre-applypatch.sample)*
*   *[**应用后补丁**](https://www.git-scm.com/docs/githooks#_post_applypatch)*
*   *[**预提交**](https://github.com/git/git/blob/master/templates/hooks--pre-commit.sample)*
*   *[**准备-提交-消息**](https://github.com/git/git/blob/master/templates/hooks--prepare-commit-msg.sample)*
*   *[**提交-消息**](https://github.com/git/git/blob/master/templates/hooks--commit-msg.sample)*
*   *[**提交后**](https://www.git-scm.com/docs/githooks#_post_commit)*
*   *[**预还原**](https://github.com/git/git/blob/master/templates/hooks--pre-rebase.sample)*
*   *[**结账后**](https://www.git-scm.com/docs/githooks#_post_checkout)*
*   *[**后合并**](https://www.git-scm.com/docs/githooks#_post_merge)*
*   *[**预接收**](https://www.git-scm.com/docs/githooks#pre-receive)*
*   *[**更新**](https://github.com/git/git/blob/master/templates/hooks--update.sample)*
*   *[**后期接收**](https://www.git-scm.com/docs/githooks#post-receive)*
*   *[**后期更新**](https://github.com/git/git/blob/master/templates/hooks--post-update.sample)*
*   *[**预自动气相色谱**](https://www.git-scm.com/docs/githooks#_pre_auto_gc)*
*   *[**后期改写**](https://www.git-scm.com/docs/githooks#_post_rewrite)*
*   *[**预推**](https://www.git-scm.com/docs/githooks#_pre_push)*

## *最后的笔记*

*Git 挂钩是一种非常有效和灵活的机制，可以改善我们的工作流程。因为它们是基于 shell 脚本的，所以几乎没有限制。我们几乎可以完成任何用其他工具不容易完成的任务。*

*请记住，Git 挂钩需要手动安装，它们不是为所有用户存储在存储库中的。我建议您在您的 Git 存储库上创建一个 *githooks/* 文件夹，并将 githooks 存储在那里。您甚至可以创建一个脚本，在第一次下载存储库时安装它们(甚至包括一个 Git 挂钩，在存储库更新后安装该文件夹中包含的所有挂钩)。*

*我在我的 [Twitter 账户](https://twitter.com/eenriquelopez)上写下我对软件工程和金融的想法。如果你喜欢这篇文章或者它确实帮助了你，请随意分享它，♥它和/或留下评论。这是给业余作家加油的货币。*