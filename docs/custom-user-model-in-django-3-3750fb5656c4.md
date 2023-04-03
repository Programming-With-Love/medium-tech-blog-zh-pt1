# Django 3 中的自定义用户模型

> 原文：<https://medium.com/quick-code/custom-user-model-in-django-3-3750fb5656c4?source=collection_archive---------0----------------------->

![](img/aa32e64e932f3795feafdf488b48193f.png)

Photo by [Kevin Ku](https://www.pexels.com/@kevin-ku-92347?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) from [Pexels](https://www.pexels.com/photo/coding-computer-data-depth-of-field-577585/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)

# 介绍

嗨，在这篇文章中，我们将学习在 Django 3 中创建一个自定义的用户模型，我们还将改变 Django 管理员的默认登录功能。我们将使用*电子邮件*和*密码*登录。

# 动机

我不得不为我的应用程序定制用户，我可以制作模型，但问题是 *createsuperuser* 命令不起作用。为了调试它，我必须做大量的研究，问题是当时的大多数资源都过时了，所以我决定写这篇文章。

我做了一个 [GitHub](https://github.com/theshubhagrwl/django_custom_user_app) repo，所以如果你想你可以直接使用它(说明在那里)

# 我们开始吧

首先做一个 **Django 项目**并创建一个名为 **users** 的 app

## 现在我们可以开始在我们的**用户**应用程序中编辑 **models.py** 文件

在编辑之前，让我们做一些理论。

Django 有哪些**经理**？

> 管理器是一个接口，通过它向 Django 模型提供数据库查询操作。Django 应用程序中的每个模型至少有一个管理器。-姜戈文件

简单地说，管理者为我们提供了一种管理模型的方法。我们可以通过使我们的模型成为 Manager 类的子类来实现这一点。manager 类是像 *createsuperuser* 这样的命令可以被编辑的地方。

现在打开 **models.py** ，把下面的代码放进去

这里最需要注意的是 *is_staff* 和 *is_superuser* 属性。在这种情况下打错字会导致调试困难。

## 我们在这里做了什么？

我们为我们的用户模型做了一个管理器。在这里面，我们做了两个函数叫做 ***create_user*** 和***create _ super user***。

*create_user* 顾名思义，创建一个新用户， *create_superuser* 用于通过将 ***is_staff*** 和 ***is_superuser*** 设置为 true 来创建一个超级用户。

在经理之后，我们有我们通常的模型。

我们将 ***用户名*** 设置为无，因为我们不想包含用户名。

其中的 ***用户名字段*** 表示我们声明的‘电子邮件’。这应该是独一无二的。

*session_token* 是可选字段。我把它放在那里，因为我正在制作我的定制令牌。

models.py 的最后一行表示 CustomUser 是 UserManager 的对象。

# 重要的事情

制作好模型后，打开 settings.py 文件，在其中添加一行

```
AUTH_USER_MODEL = 'users.CustomUser'
```

> Django 允许您通过为引用定制模型的 AUTH_USER_MODEL 设置提供一个值来覆盖默认的用户模型。这个虚线对描述了 Django 应用程序的名称(必须在 INSTALLED_APPS 中)，以及您希望用作您的用户模型的 Django 模型的名称。-姜戈文件

# 最后一步

现在，您可以运行迁移命令并创建超级用户。

```
py manage.py makemigrations
py manage.py migrate
py manage.py createsuperuser
```

它会要求你的电子邮件和密码。告诉它细节。

## 不要忘记在管理中注册应用程序

```
admin.site.register(CustomUser)
```

现在，您可以运行服务器，并在管理面板中使用您的电子邮件和密码登录。

如果你有任何建议，请告诉我。

如果有帮助，请鼓掌。

查看我的作品集:【https://theshubhagrwl.netlify.app/ 

在社交平台上与我联系:

领英:【https://www.linkedin.com/in/theshubhagrwl/ 

推特:[https://twitter.com/theshubhagrwl](https://twitter.com/theshubhagrwl)/

GitHub:[https://github.com/theshubhagrwl](https://github.com/theshubhagrwl)

insta gram:[https://www.instagram.com/theshubhagrwl/](https://www.instagram.com/theshubhagrwl/)