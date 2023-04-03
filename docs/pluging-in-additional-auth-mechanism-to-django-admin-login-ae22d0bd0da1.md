# 在 Django 管理员登录中插入额外的认证机制

> 原文：<https://medium.com/walmartglobaltech/pluging-in-additional-auth-mechanism-to-django-admin-login-ae22d0bd0da1?source=collection_archive---------0----------------------->

![](img/b0d049bf432fed44da9173aaf9349212.png)

Image credits : [https://unsplash.com/photos/aiyBwbrWWlo](https://unsplash.com/photos/aiyBwbrWWlo)

我们都非常喜欢 Django 的管理界面。对于对异步 UI 知之甚少的开发人员来说，它简化了部署应用程序的大量工作。Django 管理界面的美妙之处在于，通过一个非常简单的 UI 编码(甚至不涉及 HTML、CSS、JavaScript)，你可以调出一个 web 界面，为用户提供丰富的交互。访问管理界面的认证基于表**Django _ auth _ users**of**Django 中的 users 对象。该表上的 CRUD 操作只能通过使用 **python** 脚本“ **manage.py** ”来执行。在这篇文章中，我将介绍自定义管理界面后端逻辑访问的步骤。这将允许用户将管理员登录界面集成到任何其他身份验证方法，如 IAM。**

**我将在下面的 Django 项目目录的帮助下演示这个过程。**

```
project_name/
├── project_name/
│   ├── admin.py
│   ├── urls.py
│   ├── uwsgi.py
│   └── __init__.py
└── app/
    ├── admin.py
    ├── urls.py
    ├── models.py
    └── ...
```

**假设您的应用程序的 **models.py** 中有一个非常简单的模型。**

****app/models.py****

**那么默认的 **admin.py** 将看起来像这样。**

****app/admin.py****

**这个管理文件我们将注册一个模型，**我的模型**到管理界面，这将使模型可见，用于直接添加/更新/删除数据库的**我的模型**对象。**

**现在，我们将开始使用 Django 的定制认证后端来挂钩管理登录页面的过程。**

1.  **首先，我们将在应用程序文件夹中创建一个文件名为 **backend.py** 的文件。这将为我们的应用程序存储自定义后端逻辑，并将包含以下内容。**

****backend.py****

**这个文件包含定义两个方法 **get_user 的类的定义。**和**认证。**一旦在任何类中定义了这些方法，该类就成为认证后端的合适候选。我还有一个全局方法 **my_portal_authenticate** ，它是 Django 登录机制如何认证用户输入的用户名和密码的核心实现，并将在整个会话中维护。这里需要注意的一点是，如果用户第一次登录，我会将新用户保存到 **django_auth_user** 表中。这很重要，因为 Django 管理后端(默认的)与 Django **用户**模型是严格耦合的，我们正在覆盖默认后端的行为。这也允许服务后端检索会话用户。我们的解决方法是将管理默认站点与自定义 django **AdminSite** 对象挂钩，然后允许这个 **AdminSite** 对象使用我们刚刚在 **backend.py** 中编码的自定义后端。**

**2.创建一个登录表单来调用定制的后端功能，该功能稍后将被转换为 Admin 登录表单，在你的应用程序目录中的文件 say **login.py** 。这个文件将实现一个类，该类将创建 Django 表单来覆盖默认的登录验证方法。**

****app/login.py****

**3.使用 Django **AdminSite** 更改默认的管理员登录表单。**

****New Version of admin.py****

**4.最后一步是通知 Django urls 使用定制的登录 AdminSite 对象，而不是默认对象。去 **urls.py** 在你的项目设置中，而不是在应用程序设置中，添加以下内容。**

****urls.py****

**我们还必须更新**authentic ation _ BACKENDS**来将自定义后端更新到我们已经实现的后端。用新变量更新**项目名称/设置. py** 。**

**最后，所需的更改已经完成。**

**照常访问您的管理站点。如果是在 localhost 上运行，那就玩[**http://localhost:8000**](http://localhost:8000.)**