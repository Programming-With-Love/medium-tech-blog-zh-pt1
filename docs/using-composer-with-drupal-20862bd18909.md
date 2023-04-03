# 通过 Drupal 使用 Composer

> 原文：<https://medium.com/quick-code/using-composer-with-drupal-20862bd18909?source=collection_archive---------9----------------------->

![](img/8540ce2e466379531d3340e7a70e2a57.png)

从 8.1 版本开始，Drupal 核心接收了一个专用的包管理器[编写器](https://www.drupal.org/docs/develop/using-composer)。它有助于管理基于 PHP 的项目中的依赖关系，以及快速安装和更新库，包括第三方库(以前必须由开发人员‘手动’执行)。

如果你已经有了和类似 [Linux apt](https://itsfoss.com/apt-command-guide/) 、 [yum](http://yum.baseurl.org/) 或者 [Yarn](https://yarnpkg.com/) 这样的包管理器一起工作的经验，那么你掌握 Composer 并不困难(它基本上是 PHP 专属的常规包管理器)。

**重要提示:目前 Drupal 8 支持 PHP 5.5.9+。但是，您可能仍然需要至少版本 7 的 PHP。**

本文回顾并讨论了包管理器，并解释了如何在 Drupal 中使用 Composer。

# 将 Composer 与 Drupal 结合使用的优势

尽管 Composer 是 Drupal 核心中的一个额外功能，但它的集成已经成为 web 开发人员的普遍习惯。只有当模块的数量逐渐增加时，项目的扩展才是合理的。

还不确定你是否需要作曲家？让我们来看看好处:

*   能够管理任何核心、贡献包甚至个人开发的模块以及 JS 库；
*   使用起来比 Git 子模块简单得多；
*   可访问依赖系统，无需手动执行多次操作即可下载第三方依赖；
*   在更新过程中不需要检测变化(Composer 也会处理)；
*   D8 库越小越好；
*   通过使用特殊的 composer 插件，实现了可访问和接近全自动的修补系统。补丁直接在 composer.json 中应用；
*   能够在新项目中重复使用 composer.json(服务器上的配置文件 nginx 必须为此进行定制，但这是最容易使用的选项)。

总而言之，Composer 实际上允许对不断增长的依赖列表进行完全自动化的管理(例如，您可以自主下载和更新某些包的版本)。Composer 使用起来并不完全简单，但是一旦你弄明白了它，你就会把自己从大量繁琐的日常任务中解放出来。

# Composer 是如何工作的？

Composer 由两个文件组成:

*   **composer.json.** 该文件包含关于需要哪些特定包、它们应该安装在哪里以及其中必须包含哪些配置和脚本的数据。基本上，composer.json 提供了关于您可以在正在安装的模块中手动定制的所有内容的建议。
*   **composer.lock.** 这个是自动更新的，包含项目当前状态的所有数据。它的出现也不需要任何手动输入，也不需要定制或删除它。

# 学习使用 Composer

你已经决定开始学习在 Drupal 8 站点上使用 Composer 了吗？该工具有三个主要命令。如果你学会了和他们一起工作，你就掌握了和 Composer 一起工作的技巧。

# 作曲者需要包名

使用**composer require package name**命令安装模块。为了在一个命令中安装一组模块，只需用空格分隔定义它们的名称(模块的名称是以 vendor/name 格式定义的——这允许您排除在单个项目中出现同名包的情况)。对于来自 drupal.org 的所有项目，格式将是 drupal/PROJECT_NAME。因此，您应该输入下面的命令来安装一个商业模块——composer 需要 drupal/commerce。

# composer 更新-具有依赖性

更新与安装非常相似。这两个过程的区别在于，使用这个命令，包的名称是不必要的。 **composer update** 命令默认更新 composer.json 文件中定义的所有依赖项。也就是说，通过 Composer 进行的更新只涉及该文件中提到的包。另一方面，如果一个模块具有 composer.json 中没有定义的特殊依赖关系，那么只有在命令中额外使用以下关键短语时，它们才会被更新—**—with—dependencies**。使用**composer update-with-dependencies**来更新每个软件包，包括它们的依赖项(该命令最适合在发生次要核心版本更新时使用)。您还可以定义要更新的包的名称:composer update drupal/commerce。

# 编写器删除包名

使用**composer remove package name**命令删除包。也要注意从你的管理面板中删除所需的模块(这应该在命令输入之前完成)。

# …以及使用 Composer 的几个更重要的时刻

不幸的是，并非所有初学者都能立即意识到最佳解决方案是最初通过 Drupal 8 Composer 开始构建项目，因为官方网站上没有这方面的提示。

首先，通过这种方式，您可以忘记频繁使用的 [Drush](https://www.drupal.org/project/drush) 实用程序。其次，您不需要通过 FTP(可能只有自定义解决方案需要)或管理面板下载所有需要的模块，从而手动安装它们。第三，Composer 不仅允许下载和更新模块和主题，还允许下载和更新内核本身。如果没有 Composer(这实际上是 Drupal 第 8 版的一个标准)，您将根本无法安装某些模块(这些模块可能包括 Commerce、Search API、ImageMagick 等)。即使您处理下载和启动它们，它们仍然不会工作，因为以包的形式存储在 Composer 中的不可用的依赖项。另一方面，如果您从一开始就使用这个工具，那么项目中的依赖项将会自动定义，您不必纠结于哪些需要更新，哪些不需要更新。

我们预计未来仅通过 composer 安装的模块数量将会增加。这意味着如果您决定将 Drupal 升级到第 8 版，您将不得不学习以某种方式使用 Composer。

# Drupal 8 Composer 教程:结论

![](img/6e7082c4f8e828afc22b22461bab6915.png)

如果您使用的是早于第 8 版的 Drupal，Composer 并不是您项目的唯一解决方案。然而，最新版本为开发人员提供了实现项目功能的最大机会和自动化的最大解决方案(这反过来允许专注于更不常见的任务，而不是浪费时间在模块的连接/更新上)。这就是为什么，迟早，您将不得不面对将 Composer 与 Drupal 一起使用的必要性。这就是为什么我们建议尽早了解使用该工具的细微差别(我们希望我们简短的 Drupal 8 Composer 教程可以帮助您做到这一点),以便使您的进一步开发过程更加顺利并以结果为导向。总而言之，这将是你未来网站开发职业生涯中一次绝佳的时间投资。

对一个项目有想法吗？我们将提供其[顶级执行力](https://anyforsoft.com/)！

【anyforsoft.com】最初发表于[](https://anyforsoft.com/blog/using-composer-drupal)**。**