# 将 Django 应用程序部署到 Heroku:完整指南

> 原文：<https://medium.com/quick-code/deploying-django-app-to-heroku-full-guide-6ff7252578d7?source=collection_archive---------0----------------------->

![](img/1e7e248108809b60e6b5bbbe9e547eda.png)

你和姜戈一起做了个应用。太好了！，你很兴奋地向地球上的每个人展示它。为此，你需要把它放在网上的某个地方，以便其他人可以访问它。

好吧，你做了些调查然后选择了赫罗库。很棒的选择！👍

现在，你开始部署你的应用程序，并在网上做你的研究，现在你有许多不同的资源可以遵循，这些资源提供了数百万种方法。因此，你感到困惑、沮丧和困顿，不知何故设法把它放在主机上，但之后，令你惊讶的是，你发现你的 CSS 文件没有出现。🤦‍♂️

好，现在让我们来解决你的问题。这篇文章几乎涵盖了你需要的一切。

# 路标

*   安装所需的工具
*   创建所需的文件
*   创建 Heroku 应用程序
*   编辑设置. py
*   对静态文件进行更改

# 安装所需的工具

要部署到 Heroku，您需要安装 Heroku CLI(命令行界面)。

你可以通过这里来做到这一点:[https://devcenter.heroku.com/articles/heroku-cli](https://devcenter.heroku.com/articles/heroku-cli)

CLI 是必需的，因为它将使我们能够使用登录、运行迁移等功能。

# 创建 Heroku 需要的文件

安装完 CLI 后，让我们创建 Heroku 需要的所有文件。

文件有:

## **requirements.txt**

Requirements.txt 制作最简单。只需运行命令

```
pip freeze > requirements.txt
```

这个命令将生成一个. txt 文件，其中包含当前 Django 应用程序所需的所有包。

**注意:如果您进一步添加任何包，然后再次运行此命令，这样文件将会用新的包更新**

***requirements . txt***有什么用？
如您所见，它包含了您的应用程序所需的所有依赖项。所以当你把你的应用程序放到 Heroku 上时，它会告诉 Heroku 要安装哪些包。

## **过程文件**

之后，创建一个新的文件名 Procfile，不要添加任何扩展名。这是 Heroku 需要的文件

根据 Heroku 的说法:

> Heroku 应用程序包括一个 **Procfile** ，它指定了应用程序在启动时执行的命令。您可以使用 Procfile 来声明各种**过程类型**，包括:
> 
> 你的应用程序的 web 服务器
> 多种类型的工作进程
> 一个单独的进程，比如一个[时钟](https://devcenter.heroku.com/articles/scheduled-jobs-custom-clock-processes)
> 在一个新版本部署之前运行的任务

对于我们的应用程序，我们可以编写以下命令

```
web: gunicorn name_of_your_app.wsgi -log-file -
```

如果你对你的应用程序名称感到困惑，那么只需进入你的项目中的 wsgi.py 文件，你将在那里找到你的应用程序名称。

为此，您应该安装 gunicorn 并将其添加到 requirements.txt 文件中

安装非常简单。你一定猜到了！

```
pip install gunicorn
```

## **runtime.txt**

之后，创建一个名为 runtime.txt 的新文本文件，并在其中以如下格式编写您正在使用的 python 版本

```
python-3.8.1 
```

这是我们需要的所有文件。现在我们必须开始编辑 settings.py 文件。

# 创建 Heroku 应用程序

这是一个简单的步骤，可以通过两种方式完成，要么通过命令行，要么通过 Heroku 网站。

现在让我们使用 Heroku 网站。

*   创建 Heroku 账户后，你会看到一个创建新应用的选项
*   它会要求您输入名称，名称应该是唯一的。点击和试用后，您将被重定向至您的应用仪表板。
*   这里有许多选项，但让我们转到设置选项卡，然后单击显示配置变量
*   在密钥中写入 SECRET_KEY，在值中粘贴设置文件中的密钥，您可以更改它，因为只有这个密钥会被使用。
*   目前就这些。
*   我们很快会重访。

# 编辑设置. py

在这个文件中应该有相当多的修改。

我们开始吧

第一次改变

```
DEBUG = False
```

在允许的主机中，输入您的 Heroku 应用程序的域

```
ALLOWED_HOSTS = ["your_app_name.herokuapp.com", "127.0.0.1"]
```

用下面的内容替换 SECRET_KEY 变量(假设您已经在前面的步骤中在 heroku 中设置了密钥)

```
SECRET_KEY = os.environ.get('SECRET_KEY')
```

它的作用是从环境中获取 SECRET_KEY。在我们的例子中，我们可以在 Heroku 中设置 secret_key，它将通过环境变量在这里提供密钥。

# 设置静态文件

在设置文件中，您会发现

```
STATIC_URL = '/static/'
```

用下面的代码替换它

```
STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
STATIC_URL = '/static/'

STATICFILES_DIRS = (
    os.path.join(BASE_DIR, 'static'),
)
```

基本上，这将创建一个名为 static 的文件夹，其中包含所有静态文件，如 CSS 文件。

如果您的应用程序包含您已存储的图像，或者用户有能力存储图像，则添加以下行

```
MEDIA_URL = "/media/"
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

这和上面的差不多

你还需要做一件事。

如果你有媒体文件，那么要让 Django 提供服务，你必须在项目的 urls.py 文件中添加一行(顶级 urls 文件)

```
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    # ... the rest of your URLconf goes here ...
] + static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

我强烈建议您看一下这个文档。

https://docs.djangoproject.com/en/3.1/howto/static-files/

在生产中为静态文件提供服务最不需要的就是 WhiteNoise

> *通过几行配置，WhiteNoise 允许你的 web 应用服务于它自己的静态文件，使它成为一个独立的单元，可以部署在任何地方，而不需要依赖 nginx、亚马逊 S3 或任何其他外部服务。(在 Heroku、OpenShift 等 PaaS 提供商上特别有用。)-白化文件*

安装白噪音

```
pip install whitenoise
```

将其添加到 settings.py 文件的中间件中

```
MIDDLEWARE = [
  # 'django.middleware.security.SecurityMiddleware',
  'whitenoise.middleware.WhiteNoiseMiddleware',
  # ...
]
```

之后，不要忘记运行创建 requirements.txt 文件的命令。记得吗？

看一下文档
【http://whitenoise.evans.io/en/stable/ 

最后，我们已经完成了部署的两个最重要的步骤

# 向 GitHub 添加代码

制作一个新的 Github Repo，并在其中添加您的所有代码。
[用本帖作参考](https://dev.to/ayomide_bajo/pushing-an-existing-repository-to-github-1f68)

之后，转到 Heroku，在 Deploy 选项卡下，您会看到一个连接 Github 的选项。

连接您的 repo，您可以点击部署按钮来部署您的应用程序。

# 使用 Heroku Postgres

## 需要什么？我已经在用 SQLite 了！

问题是

> Heroku 文件系统是短暂的——这意味着在 dyno 运行期间对文件系统的任何更改都只会持续到 dyno 关闭或重启。每个 dyno 引导时都带有最近一次部署的文件系统的干净副本。这类似于 Docker 等基于容器的系统的运行数量。
> 此外，在正常操作下，dynos 每天都会在一个称为“循环”的过程中重新启动。

[https://help . heroku . com/k1 PPS 2 WM/why-are-my-file-uploads-missing-deleted](https://help.heroku.com/K1PPS2WM/why-are-my-file-uploads-missing-deleted)

基本上，你存储的所有数据每 24 小时就会被删除一次。

要解决 Heroku，建议使用 AWS 或 Postgres。Heroku 使得使用 Postgres 变得非常简单。

我们开始吧

进入你的应用仪表板，在资源部分搜索 Postgres。选择它，你会得到这样的东西

![](img/e90bca6d5bde012b15f77db432ae1b3e.png)

Postgres Add-on in Heroku

现在转到设置选项卡，显示配置变量

您将在那里看到一个 DATABASE_URL 键。这意味着 Heroku 已经添加了数据库，现在我们必须告诉我们的应用程序使用这个数据库。

为此，我们将需要另一个名为 dj_database_url 的包。通过 pip 安装并导入到 settings.py 文件的顶部

现在，将以下代码粘贴到设置文件中的数据库下

```
db_from_env = dj_database_url.config(conn_max_age=600)
DATABASES['default'].update(db_from_env)
```

现在你的数据库已经设置好了

目前，您的数据库是空的，您可能想要填充它。

*   开放终端
*   类型→ `heroku login`
*   登录后运行以下命令

```
heroku run python manage.py makemigrations
heroku run python manage.py migrate
heroku run python manage.py createsuperuser
```

现在，您的应用程序已经可以部署了

要么使用

```
git push heroku master
```

(提交更改后)或通过 Github 推送。

好了，终于完成了。这有点长，但很容易做到，因为你只需要做一些小的改变，没有太大的变化。

如果这篇文章被证明是有帮助的，那么不要忘记给它鼓掌👏

如果有任何改进，请在评论中告诉我。😄

# 查看我的其他文章

[](/@theshubhagrwl/should-you-track-your-time-by-shubh-agrawal-968aecfcf97f) [## 你应该记录你的时间吗？-舒布·阿格拉瓦尔

### 时间追踪是高效人士给出的最常见的建议…

medium.com](/@theshubhagrwl/should-you-track-your-time-by-shubh-agrawal-968aecfcf97f) [](/@theshubhagrwl/custom-user-model-in-django-3-3750fb5656c4) [## Django 3 中的自定义用户模型

### 嗨，在这篇文章中，我们将学习在 Django 3 中创建一个自定义用户模型，并使用电子邮件登录 Django admin。

medium.com](/@theshubhagrwl/custom-user-model-in-django-3-3750fb5656c4) 

# 访问我的博客

[](https://theshubhagrwl.hashnode.dev/) [## 舒布·阿格拉瓦尔的博客

### 这是舒布·阿格拉瓦尔的博客。

theshubhagrwl.hashnode.dev](https://theshubhagrwl.hashnode.dev/) 

# 查看我关于 Dev.to 的文章

[](https://dev.to/theshubhagrwl) [## Shubh Agrawal - DEV 简介

### 全栈开发者| Django | React |竞争性编程

开发到](https://dev.to/theshubhagrwl) 

# 访问我的网站，在博客和社交平台上关注我

[http://theshubhagrwl.netlify.app/](http://theshubhagrwl.netlify.app/)

*原载于 2020 年 9 月 20 日*[*https://dev . to*](https://dev.to/theshubhagrwl/deploying-django-app-to-heroku-full-guide-4ce0)*。*