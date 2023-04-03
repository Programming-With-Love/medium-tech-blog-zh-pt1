# Ruby on Rails Gems 开发的瑰宝

> 原文：<https://medium.com/quick-code/ruby-on-rails-gems-for-web-development-15eb37c4d791?source=collection_archive---------0----------------------->

![](img/feb534e65f151e54cb1d990413b9c130.png)

我在 Ruby on Rails 上编程已经很多年了，并且使用这个优秀的框架解决了各种相当复杂的任务。根据我的经验，我列出了一份我认为最有用的宝石清单。在这篇文章中，我想分享这个列表，并告诉你如何找到对 RoR 有用的宝石。

要获得关于 Ruby 的深入知识，请注册参加 [**Ruby On Rails 在线培训**](https://onlineitguru.com/ruby-on-rails-online-training-placement.html) 的现场免费演示

不幸的是，gemís 规范的格式不包括定义类别和标签。这就是为什么，当搜索宝石来完成某些任务时，我们可能只希望宝石的作者在描述中提到了一些关键字。你可以在 rubygems.org 或 github.com 找到许多宝石。搜索可以通过描述来完成(在 GitHub 上，您还必须从语言列表中选择 Ruby)。

另一个值得一提的是 Ruby 工具箱。它允许按类别和受欢迎程度搜索宝石。但是不要仅仅依赖这个来源，因为 Ruby Toolbox 的作者手工添加了新的 gem。

# 外国人

这个 gem 有助于为表创建外键。它非常容易使用。您可以简单地将它放到 Gemfile 中，它将为您的迁移添加两个新方法:`add_foreign_key`和`remove_foreign_key`。同样，你可以用`foreign_key`和`remove_foreign_key`方法从`create_table`和`change_table`中添加/删除按键。

假设我们需要从 comment 表向 posts 表添加一个键。我们可以用另一种方法:

```
class CreateComments < ActiveRecord::Migrationdef changecreate_table :comments do |t|# … t.references :post# ...t.foreign_key :postsend# …endend
```

这些方法有一些额外的选项，例如名称、列、依赖项。你可以在文档中读到更多关于它们的内容。

有人可能会说，对于 Rails 的新版本来说，这是不现实的，但它只是从 4.2 版本开始(在 4.2 版本中，这种功能已经现成可用)。在所有其他情况下，在我看来，这个宝石应该在有用的列表中。

# 开信刀

一个简单但非常有用的宝石。事实上，它是一个将电子邮件保存在文件中而不是发送电子邮件的插头。要激活这个 gem，您必须在 appís 配置中将`letter_opener`设置为交付方法(例如在 config/environment s/development . Rb 中)。

```
config.action_mailer.delivery_method = :letter_opener
```

答对了。现在，所有发出的邮件都将存储在/tmp/letter_opener 文件夹中，新邮件将在发送后立即在浏览器中预览。它既简单又实用。

# 神成

这个 gem 允许轻松创建任何复杂度的分页器。Kaminari 支持几种 ORM(active record、Mongoid、MongoMapper)和模板引擎(ERB、Haml、Slim)。

Kaminari 不嵌入基本类:数组、hash、Object 和 ActiveRecord::Base。

要开始使用 Kaminari，把它放在 Gemfile 里就够了。之后，一些功能将变得可用，例如 page、per 和 padding。现在，借助于`Kaminari.paginate_array`方法，您可以毫不费力地将您的数组转换成可分页的数组，然后许多有用的分页功能将变得可用。

```
@paginatable_array = Kaminari.paginate_array(my_array_object).page(params[:page]).per(10)
```

默认配置可以在`Kaminari.configure`初始化器中进行。`default_per_page`、`max_per_page, max_pages` -这只是可以设置的选项的简要列表。

此外，可以为每个型号单独配置分页:

如果需要定制分页器，可以通过运行生成器来创建模板:

```
% rails g kaminari:views default # -e haml - if you want to use  HAML template engine.
```

模板将在 app/views/kaminari/中创建，现在您可以轻松地编辑它们。

本地化(I18n)和标签、主题和友好的 URL，以及其他有用的选项可以在这个 gem 的文档中找到。欲了解更多信息，请访问 [**Ruby On Rails 课程**](https://onlineitguru.com/ruby-on-rails-online-training-placement.html)

# 载波波

使用 CarrierWave，您可以从您的 RoR 应用程序中上传任何文件。你需要做的就是:

创建上传者:

```
rails generate uploader ProductPhotoUploader
```