# 带有 Bitbucket 管道的 CakePHP 的& Heroku

> 原文：<https://medium.com/quick-code/ci-cd-of-cakephp-with-bitbucket-pipelines-heroku-81005ae70ff8?source=collection_archive---------5----------------------->

## 使用 Bitbucket 管道自动构建、测试和部署 CakePHP 应用程序到 Heroku。

![](img/3d0967183298df3b11154e64e862b4f6.png)

在我的上一篇文章中，我演示了如何在代码提交到 Bitbucket 后，通过 FTP 使用 Bitbucket 管道自动构建、测试和部署一个 CakePHP 应用程序到一个共享的主机提供商。今天我们将把它部署到 [Heroku](https://www.heroku.com/) ，一个云应用平台。

# 先决条件

*   [Bitbucket](https://bitbucket.org) 和 [Heroku](https://heroku.com/) 账号
*   安装在开发机上的 [Git](https://git-scm.com/) 、 [PHP](http://php.net/) 和 [Composer](https://getcomposer.org/)

# 创建位存储库

首先，我们创建一个新的[位存储库](https://bitbucket.org/)。

![](img/defff015766677f42a1f8df900af0e12.png)

Create new Bitbucket repository

# 初始化并提交 CakePHP 应用程序

在本演示中，我们通过在命令行中键入以下命令来创建一个 [skeleton CakePHP 应用程序](https://github.com/cakephp/app):

```
composer create-project --prefer-dist cakephp/app cakephp-ci-heroku-demo
```

如果您对刚刚创建的内容感兴趣，请使用内置服务器启动它，并启动您最喜欢的浏览器:

```
cd cakephp-ci-heroku-demo
bin/cake server -p 8765
```

![](img/25c0be56ca2ae6a964b20c1334831082.png)

Skeleton CakePHP app in browser

现在，我们可以将开发机器上的本地 git 存储库与 Bitbucket 中的远程存储库共享。您可以在 Bitbucket 存储库概述中找到您的 git URL。对我来说，这些命令看起来像这样:

```
# switch to porject folder in ./cakephp-ci-heroku-demogit init
git remote add origin [https://kainiklas@bitbucket.org/kainiklas/cakephp-ci-heroku-demo.git](https://kainiklas@bitbucket.org/kainiklas/cakephp-ci-demo.git)git add -A
git commit -m "Initial commit"
git push origin master
```

代码现在应该在我们的 Bitbucket 存储库中可见了。

# 创建 Heroku 应用程序

## 创建 Heroku 应用程序

现在我们创建一个新的 Heroku 应用程序。

![](img/9f3a3dd792da712dc4d5061f1c58e0e9.png)

Create new Heroku Application

**注意:**我们也可以使用 Heroku 的内置 git 存储库和管道特性，根本不用 Bitbucket。但是我更喜欢有一个包含所有特性的单独的代码库，比如拉请求。

# 初始化位桶管道

我们在 [Bitbucket](https://bitbucket.org) 上导航到我们的存储库，并在左侧导航菜单中点击 Pipelines。我们第一次点击它，需要一些时间，直到我们看到一些东西(大约 5-10 秒)。我们向下滚动页面并选择 PHP。然后我们提交文件，管道自动启动。

起初，它看起来很好，但后来管道出现了一些问题，我们将在以后修复这些问题。

```
#1: cakephp/cakephp 3.5.11 requires ext-intl * -> the requested PHP extension intl is missing from your system.#2: symfony/debug v4.0.3 requires php ^7.1.3 -> your PHP version (7.1.1) does not satisfy that requirement.And 5 more.
```

![](img/00ba0973919a61d3ab6fefe8f753ef90.png)

First Pipeline run fails

# 为 CakePHP & Heroku 配置 Bitbucket 管道

我们的管道将首先通过 composer 为 CakePHP 应用程序安装所有的依赖项，然后运行所有的 [PHPUnit](https://phpunit.de/) 测试。如果一切正常，我们将代码推到 Heroku git 库的头部，以便在 Heroku 上部署。Heroku git 存储库的通用 URL 如下所示:

```
https://heroku:$HEROKU_API_KEY@git.heroku.com/$HEROKU_APP_NAME.git
```

**注意:**美元符号`$`表示 Bitbucket 管道注释中的变量名。

## 在 Bitbucket 中设置环境变量

上面的 URL 包含一个 API 键，不应该在任何地方硬编码。所以我们将其配置为 Bitbucket 中的环境变量。我们在 [Heroku 账户](https://dashboard.heroku.com/account)页面上找到了我们的 API 密匙。可以在存储库的 Bitbucket 中的设置页面上设置环境变量。不要忘记选中“安全”框来安全地存储 API 密钥。

![](img/9e0e5278405d767d14e926d57ba12a42.png)

Configure the environment variable

**重要提示:**切勿在 git 中存储密码或其他机密信息。相反，利用例如安全存储的环境变量作为密码。

## 配置管道

现在让我们检查一下我们之前在 Bitbucket 中生成的管道文件，并为 CakePHP 配置它:

```
# switch to ./cakephp-ci-heroku-demogit pull origin master
```

打开文件`bitbucket-pipelines.yml`进行配置。

## 已配置管道的说明

通过`image: edbizarro/bitbucket-pipelines-php7`,我们告诉管道哪个 docker 容器应该用于构建。对于 CakePHP，我们需要默认 PHP 映像提供的更高版本的 PHP 7。此外，我们需要使用默认的 php 7 容器安装`composer`和 PHP 扩展`intl`。否则我们的构建将会失败，就像我们之前看到的那样。docker image[edbizarro/bit bucket-pipelines-php 7](https://github.com/edbizarro/bitbucket-pipelines-php7)包含必要的工具和 PHP 扩展(以及演示中不需要的更多内容)。

使用`branches: master:`我们定义，一旦我们将代码签入主分支，管道就应该运行。

使用`caches: -composer`,我们缓存所有在依赖项安装期间下载的 composer 包。这将加快后续运行的速度。

最后一部分是`script:`部分的脚本。我们 1)安装所有需要的模块，2)测试应用程序，3)将它部署到 Heroku。如果任何命令失败，整个管道都会失败。这意味着，失败的测试将阻止部署到服务器上(这很好，因为我们不想中断生产)。

部署语句利用了我们的环境变量。我们可以通过添加一个`$`和已配置变量的名称来使用任何已设置的变量。

## 补充 composer.json 描述符

在我们将任何东西部署到产品中之前，我们希望运行所有 PHPUnit 测试，以确保一切正常。因此，我们需要将它包含在我们的 composer 文件中:

```
composer require --dev phpunit/phpunit
```

# 测试管道

测试我们的管道非常容易。我们只需要更改一个文件，并将其推送到主分支。由于我们还没有将任何文件签入到我们的存储库中，我们现在可以通过签入我们的工作来测试管道:

```
# switch to ./cakephp-ci-heroku-demogit add -A
git push origin master
```

管道现在应该是绿色的，并将代码推送到 heroku。Heroku 自动检测到推送的应用程序是一个 PHP 应用程序，因为它包含一个`composer.json`文件，并使用以下命令“安装”该应用程序:

```
composer install --no-dev --prefer-dist --optimize-autoloader --no-interaction
```

**注意:**如果你想知道为什么演示应用程序会产生错误(“应用程序试图从*调试工具*插件加载文件”)，这是因为*调试工具*是开发依赖的一部分，不会被安装。这是一件好事，因为我们不想让调试信息公开。

关于 Heroku 上 PHP 的更多信息可以在下面的帮助页面中找到:

*   [https://devcenter.heroku.com/articles/php-support](https://devcenter.heroku.com/articles/php-support)
*   [https://dev center . heroku . com/articles/getting-started-with-PHP](https://devcenter.heroku.com/articles/getting-started-with-php)
*   [https://devcenter.heroku.com/articles/deploying-php](https://devcenter.heroku.com/articles/deploying-php)

# 进一步阅读

[![](img/783a86ddd21b8f716482e9175a51447d.png)](http://books.dev-insights.com/books/link/0321336380)[![](img/126b97d8d87c4e8126854278a99cacb1.png)](http://books.dev-insights.com/books/link/0321601912)[![](img/7a42dd67eb9bac48946c17021cb243fc.png)](http://books.dev-insights.com/books/link/0470872497)

Further readings

# 想多读点？

订阅我关于现代软件架构的 [**简讯**](https://mailchi.mp/7f46c21dfc63/better-software-architect) 。增长您的技能，成为更好的软件架构师。

支持我的工作，看看我的书“[成为一个更好的软件架构师——实践经验中的行动和见解](http://bit.ly/betterSoftwareArchitect)”。

![](img/231d357c4a1436e2c1d7f572353d2cbf.png)

[Become a Better Software Architect — Actions and insights from practical experience](http://bit.ly/betterSoftwareArchitect)

*原载于 2018 年 2 月 1 日*[*kai-niklas . de*](https://kai-niklas.de/152/ci-cd-of-cakephp-with-bitbucket-pipelines-heroku/)*。*