# 升级 Ruby on Rails

> 原文：<https://medium.com/quick-code/upgrading-ruby-on-rails-8773e43c45ef?source=collection_archive---------0----------------------->

本指南提供了将应用程序升级到较新版本的 Ruby on Rails 时需要遵循的步骤。

![](img/18065ffca2e2059d48ad34133fcdd9f7.png)

# 1 一般建议

在尝试升级现有应用程序之前，您应该确保您有一个好的升级理由。您需要平衡几个因素:对新特性的需求、寻找旧代码支持的难度增加，以及您可用的时间和技能，等等。

## 1.1 测试覆盖率

确保您的应用程序在升级后仍然工作的最好方法是在开始升级之前进行充分的测试。如果你没有测试你的应用程序的自动化测试，你将需要花费时间手工测试所有已经改变的部分。在 Rails 升级的情况下，这将意味着应用程序中的每一项功能。帮你自己一个忙，确保你的测试覆盖率是好的*在*你开始升级之前。

## 1.2 升级过程

当更改 Rails 版本时，最好慢慢来，一次一个次要版本，以便充分利用弃用警告。Rails 版本号的格式是 Major.Minor.Patch。允许主版本和次版本对公共 API 进行更改，因此这可能会导致应用程序出错。补丁版本只包含 bug 修复，不改变任何公共 API。更多信息请访问 [**Ruby On Rails 在线培训**](https://onlineitguru.com/ruby-on-rails-online-training-placement.html)

**该过程应该如下进行:**

1.  编写测试并确保它们通过。
2.  在您的当前版本之后，移动到最新的补丁版本。
3.  修复测试和不推荐使用的功能。
4.  移动到下一个次要版本的最新补丁版本。

重复这个过程，直到达到目标 Rails 版本。每次移动版本时，您都需要在`Gemfile`(可能还有其他 gem 版本)中更改 Rails 版本号并运行`bundle update`。然后运行下面提到的更新任务来更新配置文件，然后运行您的测试。

您可以在这里找到所有已发布 Rails 版本的列表。

## 1.3 Ruby 版本

Rails 通常与最新发布的 Ruby 版本保持一致:

*   Rails 6 需要 Ruby 2.5.0 或更新版本。
*   Rails 5 需要 Ruby 2.2.2 或更新版本。
*   Rails 4 偏好 Ruby 2.0，需要 1.9.3 或更新版本。
*   Rails 3.2.x 是最后一个支持 Ruby 1.8.7 的分支。
*   Rails 3 及以上版本需要 Ruby 1.8.7 或更高版本。对所有以前的 Ruby 版本的支持已经被正式取消了。你应该尽早升级。

Ruby 1.8.7 p248 和 p249 有编组错误，导致 Rails 崩溃。自 1 . 8 . 7–2010.02 发布以来，Ruby Enterprise Edition 已经修复了这些问题。在 1.9 版本方面，Ruby 1.9.1 不可用，因为它完全存在缺陷，所以如果你想使用 1.9.x 版本，可以直接跳到 1.9.3 版本。

## 1.4 更新任务

Rails 提供了`app:update`命令(4.2 及更早版本中的`rake rails:update`)。在`Gemfile`中更新 Rails 版本后，运行这个命令。这将帮助您在交互式会话中创建新文件和更改旧文件。

`$ rails app:update`

`identical config/boot.rb`

`exist config`

`conflict config/routes.rb`

`Overwrite /myapp/config/routes.rb? (enter "h" for help) [Ynaqdh]`

`force config/routes.rb`

`conflict config/application.rb`

`Overwrite /myapp/config/application.rb? (enter "h" for help) [Ynaqdh]`

`force config/application.rb`

`conflict config/environment.rb`

`...`

不要忘了检查差异，看看是否有任何意想不到的变化。

## 1.5 配置框架默认值

新的 Rails 版本可能与以前的版本有不同的配置默认值。然而，在执行了上述步骤之后，您的应用程序仍然会使用*以前的* Rails 版本的默认配置运行。这是因为`config/application.rb`中的`config.load_defaults`的值尚未更改。

为了允许您逐个升级到新的默认值，更新任务创建了一个文件`config/initializers/new_framework_defaults.rb`。一旦您的应用程序准备好使用新的默认值运行，您就可以删除这个文件并翻转`config.load_defaults`值。

# 2 从 Rails 5.2 升级到 Rails 6.0

有关 Rails 6.0 更改的更多信息，请参见发行说明。

## 2.1 使用 Webpacker

Webpacker 是 Rails 6 的默认 JavaScript 编译器。但如果是升级 app，默认不激活。如果您想使用 Webpacker，那么将它包含在您的 Gemfile 中并安装它:

`gem *"webpacker"*`

`bin/rails webpacker:install`

## 2.2 强制 SSL

控制器上的`force_ssl`方法已经被弃用，并将在 Rails 6.1 中被移除。我们鼓励您在整个应用程序中启用`config.force_ssl`来实施 HTTPS 连接。如果您需要免除某些端点的重定向，您可以使用`config.ssl_options`来配置该行为。

## 2.3 签名或加密 cookie 中的目的现已嵌入 cookie 中

为了提高安全性，Rails 将目的信息嵌入到加密或签名的 cookies 值中。Rails 可以阻止试图复制 cookie 的签名/加密值并将其用作另一个 cookie 的值的攻击。

这种新嵌入信息使得这些 cookies 与 6.0 之前的 Rails 版本不兼容。

如果您要求您的 cookies 由 Rails 5.2 和更早版本读取，或者您仍在验证您的 6.0 部署并希望能够回滚，请将`Rails.application.config.action_dispatch.use_cookies_with_metadata`设置为`false`。

## 2.4 所有 npm 包都已被移到`@rails`范围

如果您之前通过 npm/yarn 加载了任何一个`actioncable`、`activestorage`或`rails-ujs`包，那么您必须更新这些依赖项的名称，然后才能将它们升级到`6.0.0`:

`actioncable → @rails/actioncable`

`activestorage → @rails/activestorage`

`rails-ujs → @rails/ujs`

## 2.5 Action Cable JavaScript API 变化

Action Cable JavaScript 包已经从 CoffeeScript 转换到 ES2015，我们现在在 npm 发行版中发布源代码。

此版本包括对 Action Cable JavaScript API 可选部分的一些突破性更改:

*   WebSocket 适配器和 logger 适配器的配置已从`ActionCable`的属性移至`ActionCable.adapters`的属性。如果您正在配置这些适配器，您将需要进行以下更改:

`- ActionCable.WebSocket = MyWebSocket`

`+ ActionCable.adapters.WebSocket = MyWebSocket`

`- ActionCable.logger = myLogger`

`+ ActionCable.adapters.logger = myLogger`

*   `ActionCable.startDebugging()`和`ActionCable.stopDebugging()`方法已被移除，并替换为属性`ActionCable.logger.enabled`。如果您正在使用这些方法，您将需要进行这些更改:

`- ActionCable.startDebugging()`

`+ ActionCable.logger.enabled = true`

`- ActionCable.stopDebugging()`

`+ ActionCable.logger.enabled = false`

[](https://onlineitguru.com/interviewquestions/ruby-on-rails-interview-questions) [## Ruby on Rails 面试问答- 2019 | OnlineITGuru

### 你是正确的地方，如果你正在寻找 Ruby on Rails 面试问题和答案，获得更多的信心来破解…

onlineitguru.com](https://onlineitguru.com/interviewquestions/ruby-on-rails-interview-questions) 

## 2.6 `ActionDispatch::Response#content_type`现在原样返回内容类型头。

以前，`ActionDispatch::Response#content_type`返回值不包含字符集部分。此行为更改为返回包含字符集部分的内容类型头。

如果你想要的只是 MIME 类型，请使用`ActionDispatch::Response#media_type`来代替。

**之前:**

`resp = ActionDispatch::Response.new(200, *"Content-Type"*` `=> *"text/csv; header=present; charset=utf-16"*)`

`resp.content_type #=> "text/csv; header=present"`

**之后:**

`resp = ActionDispatch::Response.new(200, *"Content-Type"*`

`resp.content_type #=> "text/csv; header=present; charset=utf-16"`

`resp.media_type #=> "text/csv"`

## 2.7 自动装载

Rails 6 的默认配置

`# config/application.rb`

`config.load_defaults *"6.0"*`

启用 CRuby 上的`zeitwerk`自动装载模式。在这种模式下，自动加载、重新加载和快速加载由 Zeitwerk 管理。

**2.7.1 公共 API**

一般来说，应用程序不需要直接使用 Zeitwerk 的 API。Rails 根据现有的契约进行设置:`config.autoload_paths`、`config.cache_classes`等。

虽然应用程序应该坚持使用这个接口，但是实际的 Zeitwerk loader 对象可以像

`Rails.autoloaders.main`

例如，如果您需要预加载 STI 或配置一个定制的偏转器，这可能会很方便。

**2.7.2 项目结构**

如果正在升级的应用程序自动加载正确，项目结构应该已经基本兼容。

然而，`classic`模式从丢失的常量名(`underscore`)推断文件名，而`zeitwerk`模式从文件名(`camelize`)推断常量名。这些助手并不总是彼此相反，尤其是在涉及到首字母缩写词的情况下。比如`"FOO".underscore`是`"foo"`，但是`"foo".camelize`是`"Foo"`，不是`"FOO"`。

**兼容性可以通过** `**zeitwerk:check**` **任务进行检查:**

`$ bin/rails zeitwerk:check`

`Hold on, I am eager loading the application.`

`All is good!`

**2.7.3 需求 _ 依赖**

所有已知的`require_dependency`用例都已经被删除，你应该 grep 这个项目并删除它们。

如果您的应用程序有 STI，请查看指南自动加载和重新加载常量(Zeitwerk 模式)中的 STI 部分。

**2.7.4 类和模块定义中的限定名**

现在，您可以在类和模块定义中可靠地使用常量路径:

`# Autoloading in this class' body matches Ruby semantics now.`

`class` `Admin::UsersController < ApplicationController`

`# ...`

`end`

需要注意的一个问题是，根据执行的顺序，传统的自动加载程序有时能够自动加载`Foo::Wadus`

`class` `Foo::Bar`

`Wadus`

`end`

这不符合 Ruby 语义，因为`Foo`不在嵌套中，在`zeitwerk`模式下根本无法工作。如果您发现这种情况，您可以使用限定名`Foo::Wadus`:

`class` `Foo::Bar`

`Foo::Wadus`

`end`

**或添加** `**Foo**` **到嵌套:**

`module` `Foo`

`class` `Bar`

`Wadus`

`end`

`end`

**2.7.5 关注点**

您可以从标准结构中自动加载和快速加载，如

`app/models`

`app/models/concerns`

在这种情况下，`app/models/concerns`被认为是一个根目录(因为它属于自动加载路径)，它作为名称空间被忽略。所以，`app/models/concerns/foo.rb`应该定义`Foo`，而不是`Concerns::Foo`。

作为实现的副作用，`Concerns::`名称空间与经典的自动加载器一起工作，但这并不是真正想要的行为。使用`Concerns::`的应用程序需要重命名这些类和模块，以便能够在`zeitwerk`模式下运行。

**2.7.6 自动加载路径**中有 `**app**`

**一些项目希望用类似于`app/api/base.rb`的东西来定义`API::Base`，并在`classic`模式下将`app`添加到自动加载路径中来完成。由于 Rails 会自动将`app`的所有子目录添加到自动加载路径中，所以我们会遇到另一种情况，即存在嵌套的根目录，这样安装程序就不再工作了。类似的原理我们在上面用`concerns`解释过。**

**如果您想保留这个结构，您需要从初始化器的自动加载路径中删除子目录:**

**`ActiveSupport::Dependencies.autoload_paths.delete(*"#{Rails.root}/app/api"*)`**

****2.7.7 自动加载的常量和显式名称空间****

**如果在文件中定义了名称空间，如`Hotel`所示:**

**`app/models/hotel.rb # Defines Hotel.`**

**`app/models/hotel/pricing.rb # Defines Hotel::Pricing.`**

**必须使用`class`或`module`关键字设置`Hotel`常量。例如:**

**`class`**

**`end`**

**很好。**

**替代品，如**

**`Hotel = Class.new`**

**或者**

**`Hotel = Struct.new`**

**不行，像`Hotel::Pricing`这样的子对象是找不到的。**

**此限制仅适用于显式命名空间。没有定义名称空间的类和模块可以使用这些习惯用法来定义。**

****2.7.8 一个文件，一个常量(在同一顶层)****

**在`classic`模式中，你可以在同一个顶层定义几个常量，并重新加载它们。例如，鉴于**

**`# app/models/foo.rb`**

**`class` `Foo`**

**`end`**

**`class` `Bar`**

**`end`**

**虽然`Bar`无法自动加载，但自动加载`Foo`也会将`Bar`标记为自动加载。在`zeitwerk`模式下不是这样，你需要将`Bar`移动到它自己的文件`bar.rb`中。一个文件，一个常量。**

**这仅影响与上例相同的顶级常量。内部类和模块都可以。例如，考虑**

**`# app/models/foo.rb`**

**`class` `Foo`**

**`class`**

**`end`**

**`end`**

**如果应用程序重新加载`Foo`，它也会重新加载`Foo::InnerClass`。**

**2.7.9 弹簧与`test`环境**

**如果发生变化，Spring 会重新加载应用程序代码。在`test`环境中，您需要启用重新加载来工作:**

**`# config/environments/test.rb`**

**`config.cache_classes = false`**

**否则，您会得到以下错误:**

**`reloading is disabled because config.cache_classes is true`**

**启动快照**

****Bootsnap 版本至少应为 1.4.2。****

**除此之外，如果运行 Ruby 2.5，Bootsnap 需要禁用 iseq 缓存，因为解释器中有一个 bug。在这种情况下，请确保至少依赖于 Bootsnap 1.4.4。**

****2 . 7 . 11****

**新的配置点**

**`config.add_autoload_paths_to_load_path`**

**默认情况下，`true`是为了向后兼容，但是允许你选择不添加自动加载路径到`$LOAD_PATH`。**

**这在大多数应用程序中是有意义的，因为你不应该在`app/models`中需要一个文件，例如，Zeitwerk 只在内部使用绝对文件名。**

**通过选择退出，您可以优化`$LOAD_PATH`查找(检查更少的目录)，并节省 Bootsnap 工作和内存消耗，因为它不需要为这些目录构建索引。**

****2.7.12 线程安全****

**在经典模式下，持续的自动加载并不是线程安全的，尽管 Rails 有锁，比如在启用自动加载时使 web 请求线程安全，这在`development`模式下很常见。**

**在`zeitwerk`模式下，持续自动加载是线程安全的。例如，你现在可以自动加载由`runner`命令执行的多线程脚本。**

****2 . 7 . 13 Globs in config . autoload _ paths****

**小心以下配置**

**`config.autoload_paths += Dir[*"#{config.root}/lib/**/"*]`**

**`config.autoload_paths`的每个元素都应该代表顶级名称空间(`Object`)，因此它们不能嵌套(除了上面解释的`concerns`目录)。**

**要解决这个问题，只需删除通配符:**

**`config.autoload_paths << *"#{config.root}/lib"*`**

****2.7.14 热切加载和自动加载一致****

**在`classic`模式下，如果`app/models/foo.rb`定义了`Bar`，你将无法自动加载该文件，但是急切加载将会工作，因为它会盲目地递归加载文件。这可能是一个错误的来源，如果您测试的东西第一次急切加载，执行可能会失败以后自动加载。**

**在`zeitwerk`模式下，两种加载模式是一致的，它们在相同的文件中失败和出错。**

****2.7.15 如何在 Rails 6 中使用经典自动加载器****

**通过这样设置`config.autoloader`，应用程序可以加载 Rails 6 默认值，并且仍然使用经典的自动加载程序:**

**`# config/application.rb`**

**`config.load_defaults *"6.0"*`**

**`config.autoloader = **:classic**`**

**在 Rails 6 应用程序中使用经典自动加载器时，出于线程安全考虑，建议在开发环境中将 web 服务器和后台处理器的并发级别设置为 1。**

**[](https://onlineitguru.com/blog/ruby-vs-python) [## Ruby Vs Python |和 Python 的区别| OnlineITGuru

### Ruby 是灵活的，它赋予程序员权力。有了 Ruby on rails，我们可以设计许多漂亮的网站…

onlineitguru.com](https://onlineitguru.com/blog/ruby-vs-python) 

## 2.8 主动存储分配行为变化

在 Rails 5.2 中，分配给用附加的新文件`has_many_attached`声明的附件集合:

`class` `User < ApplicationRecord`

`has_many_attached **:highlights**`

`end`

`user.highlights.attach(filename: *"funky.jpg"*, ...)`

`user.higlights.count # => 1`

`blob = ActiveStorage::Blob.create_after_upload!(filename: *"town.jpg"*, ...)`

`user.update!(highlights: [ blob ])`

`user.highlights.count # => 2`

`user.highlights.first.filename # => "funky.jpg"`

`user.highlights.second.filename # => "town.jpg"`

对于 Rails 6.0 的默认配置，分配给附件集合会替换现有文件，而不是附加到它们。这与分配给集合关联时的活动记录行为相匹配:

`user.highlights.attach(filename: *"funky.jpg"*, ...)`

`user.highlights.count # => 1`

`blob = ActiveStorage::Blob.create_after_upload!(filename: *"town.jpg"*, ...)`

`user.update!(highlights: [ blob ])`

`user.highlights.count # => 1`

`user.highlights.first.filename # => "town.jpg"`

`#attach`可用于在不删除现有附件的情况下添加新附件:

`blob = ActiveStorage::Blob.create_after_upload!(filename: *"town.jpg"*, ...)`

`user.highlights.attach(blob)`

`user.highlights.count # => 2`

`user.highlights.first.filename # => "funky.jpg"`

`user.highlights.second.filename # => "town.jpg"`

通过将`config.active_storage.replace_on_assign_to_many`设置为`true`来选择新的默认行为。在 Rails 6.1 中，旧的行为将被弃用，并在后续版本中被移除。

# 3 从 Rails 5.1 升级到 Rails 5.2

有关 Rails 5.2 变更的更多信息，请参见发行说明。

## 3.1 启动快照

Rails 5.2 在新生成的 app 的 Gemfile 中增加了 bootsnap gem。`app:update`命令在`boot.rb`中设置它。如果你想使用它，那么把它添加到 Gemfile 中，否则把`boot.rb`改成不使用 bootsnap。

## 3.2 签名或加密 cookie 中的到期现在嵌入在 cookie 值中

为了提高安全性，Rails 现在还在加密或签名的 cookies 值中嵌入到期信息。

这种新嵌入信息使得这些 cookies 与 5.2 之前的 Rails 版本不兼容。

如果您需要 5.1 及更早版本读取您的 cookies，或者您仍在验证您的 5.2 部署，并希望允许您回滚，请将`Rails.application.config.action_dispatch.use_authenticated_cookie_encryption`设置为`false`。

# 4 从 Rails 5.0 升级到 Rails 5.1

有关 Rails 5.1 变更的更多信息，请参见发行说明。

## 4.1 顶级`HashWithIndifferentAccess`已被弃用

如果您的应用程序使用顶级的`HashWithIndifferentAccess`类，您应该慢慢地将代码改为使用`ActiveSupport::HashWithIndifferentAccess`。

它只是不推荐使用的，这意味着您的代码目前不会中断，也不会显示不推荐使用的警告，但是将来会移除该常量。

此外，如果您有包含这种对象的转储的非常旧的 YAML 文档，您可能需要再次加载和转储它们，以确保它们引用正确的常数，并且加载它们在将来不会中断。

## 4.2 `application.secrets`现在加载所有按键作为符号

如果您的应用程序在`config/secrets.yml`中存储了嵌套配置，那么现在所有的键都作为符号加载，所以使用字符串的访问应该被改变。

**来自:**

`Rails.application.secrets[**:smtp_settings**][*"address"*]`

**至:**

`Rails.application.secrets[**:smtp_settings**][**:address**]`

## 4.3 删除了对`render`中`:text`和`:nothing`的不推荐支持

如果您的视图正在使用`render :text`，它们将不再工作。呈现 MIME 类型为`text/plain`的文本的新方法是使用`render :plain`。

类似地，`render :nothing`也被删除，您应该使用`head`方法来发送只包含头的响应。例如，`head :ok`发送了一个 200 的响应，但没有要渲染的正文。

# 5 从 Rails 4.2 升级到 Rails 5.0

有关 Rails 5.0 更改的更多信息，请参见发行说明。

## 需要 5.1 Ruby 2.2.2+版本

从 Ruby on Rails 5.0 开始，Ruby 2.2.2+是唯一受支持的 Ruby 版本。在继续之前，请确保您使用的是 Ruby 2.2.2 或更高版本。

## 5.2 活动记录模型现在默认从 ApplicationRecord 继承

在 Rails 4.2 中，活动记录模型继承自`ActiveRecord::Base`。在 Rails 5.0 中，所有模型都继承了`ApplicationRecord`。

`ApplicationRecord`是所有应用模型的新超类，类似于应用控制器子类`ApplicationController`而不是`ActionController::Base`。这为应用程序提供了配置应用程序范围内模型行为的单一位置。

当从 Rails 4.2 升级到 Rails 5.0 时，需要在`app/models/`中创建一个`application_record.rb`文件，并添加以下内容:

`class ApplicationRecord < ActiveRecord::Base`

`self.abstract_class = true`

`end`

然后确保你所有的模型都继承它。

## 5.3 通过`throw(:abort)`停止回调链

在 Rails 4.2 中，当一个‘before’回调在活动记录和活动模型中返回`false`时，整个回调链就会暂停。换句话说，不执行连续的“before”回调，也不执行包装在回调中的操作。

在 Rails 5.0 中，在活动记录或活动模型回调中返回`false`不会有暂停回调链的副作用。相反，回调链必须通过调用`throw(:abort)`显式停止。

当您从 Rails 4.2 升级到 Rails 5.0 时，在这种回调中返回`false`仍然会暂停回调链，但是您会收到一个关于这个即将到来的变化的反对警告。

准备就绪后，您可以选择新的行为，并通过将以下配置添加到您的`config/application.rb`中来删除不赞成警告:

`ActiveSupport.halt_callback_chains_on_return_false = false`

请注意，此选项不会影响活动支持回调，因为它们不会在返回任何值时暂停链。

## 5.4 ActiveJob 现在默认从 ApplicationJob 继承

在 Rails 4.2 中，活动作业继承自`ActiveJob::Base`。在 Rails 5.0 中，这种行为已经改变为现在从`ApplicationJob`继承。

当从 Rails 4.2 升级到 Rails 5.0 时，需要在`app/jobs/`中创建一个`application_job.rb`文件，并添加以下内容:

`class ApplicationJob < ActiveJob::Base`

`end`

然后确保您的所有作业类都继承自它。

## 5.5 轨道控制器测试

5.5.1 提取一些辅助方法到`rails-controller-testing`

`assigns`和`assert_template`已经被提取到`rails-controller-testing`宝石中。要在控制器测试中继续使用这些方法，请将`gem 'rails-controller-testing'`添加到`Gemfile`中。

如果您使用 Rspec 进行测试，请参阅 gem 文档中要求的额外配置。

5.5.2 上传文件时的新行为

如果您在测试中使用`ActionDispatch::Http::UploadedFile`来上传文件，您将需要改为使用类似的`Rack::Test::UploadedFile`类。

## 5.6 在生产环境中引导后，自动加载被禁用

默认情况下，在生产环境中引导后，自动加载现已禁用。

急切地加载应用程序是引导过程的一部分，所以顶级常量很好，仍然是自动加载的，不需要它们的文件。

更深层次的常量只在运行时执行，就像常规的方法体一样，也很好，因为定义它们的文件在启动时就已经被加载了。

对于大多数应用程序来说，这种改变不需要任何操作。但是在极少数情况下，您的应用程序在生产模式下运行时需要自动加载，请将`Rails.application.config.enable_dependency_loading`设置为 true。

## 5.7 XML 序列化

`ActiveModel::Serializers::Xml`已经从导轨上提取到`activemodel-serializers-xml`宝石上。要继续在您的应用程序中使用 XML 序列化，请将`gem 'activemodel-serializers-xml'`添加到您的`Gemfile`中。

## 5.8 删除了对传统`mysql`数据库适配器的支持

Rails 5 取消了对传统数据库适配器`mysql`的支持。大多数用户应该可以使用`mysql2`来代替。当我们找到人来维护它时，它将被转换成一个单独的宝石。

## 5.9 删除了对调试器的支持

`debugger`不被 Rails 5 所要求的 Ruby 2.2 支持。用`byebug`代替。

## 5.10 使用`rails`运行任务和测试

Rails 5 增加了通过`bin/rails`而不是 rake 运行任务和测试的能力。一般来说，这些变化是与 rake 并行的，但有些是一起移植过来的。由于`rails`命令已经寻找并运行了`bin/rails`，我们建议您在“bin/rails”上使用较短的`rails`。

要使用新的测试运行器，只需输入`rails test`。

`rake dev:cache`现在是`rails dev:cache`。

在您的应用程序目录中运行`rails`,查看可用的命令列表。

## 5.11 `ActionController::Parameters`不再继承`HashWithIndifferentAccess`

在你的应用程序中调用`params`现在将返回一个对象而不是一个散列。如果您的参数已经被允许，那么您就不需要做任何更改。如果你正在使用`map`和其他依赖于能够读取散列的方法，而不管`permitted?`如何，你将需要升级你的应用程序以首先允许，然后转换成散列。

`params.permit([:proceed_to, :return_to]).to_h`

## 5.12 `protect_from_forgery`现在默认为`prepend: false`

`protect_from_forgery`默认为`prepend: false`，这意味着它将被插入到你在应用程序中调用它的回调链中。如果您希望`protect_from_forgery`总是首先运行，那么您应该将您的应用程序改为使用`protect_from_forgery prepend: true`。

[](https://onlineitguru.com/blog/why-upgrade-your-application-to-ruby-on-rails-5-2) [## 为什么要将您的应用程序升级到 ruby on rails 5.2 | OnlineITGuru

### Ruby On Rails 是一个服务器端的 web 应用程序框架。它是用 Ruby 语言写的。Rail 是一个模型视图…

onlineitguru.com](https://onlineitguru.com/blog/why-upgrade-your-application-to-ruby-on-rails-5-2) 

## 5.13 默认模板处理程序现在是原始的

扩展名中没有模板处理程序的文件将使用 raw 处理程序呈现。以前，Rails 会使用 ERB 模板处理程序来呈现文件。

如果您不希望通过 raw 处理程序处理您的文件，您应该为您的文件添加一个可以被适当的模板处理程序解析的扩展名。

## 5.14 为模板依赖项添加了通配符匹配

现在，您可以对模板依赖项使用通配符匹配。例如，如果您这样定义模板:

`<%` `# Template Dependency: recordings/threads/events/subscribers_changed %>`

`<%`

`<%` `# Template Dependency: recordings/threads/events/uncompleted %>`

现在，您只需使用通配符调用依赖项一次。

`<%` `# Template Dependency: recordings/threads/events/* %>`

## 5.15 `ActionView::Helpers::RecordTagHelper`移动到外部宝石(记录 _ 标签 _ 助手)

`content_tag_for`和`div_for`已被删除，只使用`content_tag`。要继续使用旧方法，请将`record_tag_helper`宝石添加到您的`Gemfile`中:

`gem *'record_tag_helper'*, *'~> 1.0'*`

## 5.16 移除对`protected_attributes`宝石的支撑

导轨 5 不再支持`protected_attributes`宝石。

## 5.17 移除对`activerecord-deprecated_finders`宝石的支撑

导轨 5 不再支持`activerecord-deprecated_finders`宝石。

## 5.18 `ActiveSupport::TestCase`默认测试顺序现在是随机的

当在您的应用程序中运行测试时，默认的顺序是`:random`而不是`:sorted`。使用以下配置选项将其设置回`:sorted`。

`# config/environments/test.rb`

`Rails.application.configure do`

`config.active_support.test_order = **:sorted**`

`end`

## 5.19 `ActionController::Live`变成了`Concern`

如果您将`ActionController::Live`包含在控制器中的另一个模块中，那么您也应该用`ActiveSupport::Concern`来扩展该模块。或者，一旦包含了`StreamingSupport`，你可以使用`self.included`钩子将`ActionController::Live`直接包含到控制器中。

这意味着，如果您的应用程序过去有自己的流模块，以下代码将在生产模式下中断:

`# This is a work-around for streamed controllers performing authentication with Warden/Devise.`

`# See https://github.com/plataformatec/devise/issues/2332`

`# Authenticating in the router is another solution as suggested in that issue`

`class`

`include ActionController::Live # this won't work in production for Rails 5`

`# extend ActiveSupport::Concern # unless you uncomment this line.`

`def`

`super(name)`

`rescue` `ArgumentError => e`

`if` `e.message == *'uncaught throw :warden'*`

`throw` `**:warden**`

`else`

`raise` `e`

`end`

`end`

`end`

## 5.20 新的框架默认值

**5.20.1 活动记录** `**belongs_to**` **默认为必填选项**

如果关联不存在，默认情况下会触发验证错误。

这可以通过`optional: true`关闭。

此默认值将在新应用程序中自动配置。如果现有的应用程序想要添加这个功能，它将需要在初始化打开。

`config.active_record.belongs_to_required_by_default = true`

**5.20.2 每张 CSRF 代币**

Rails 5 现在支持基于表单的 CSRF 令牌，以减轻 JavaScript 创建的表单的代码注入攻击。打开此选项后，应用程序中的每个窗体都将拥有自己的 CSRF 令牌，该令牌特定于该窗体的操作和方法。

`config.action_controller.per_form_csrf_tokens = true`

**5.20.3 带原产地检查的防伪保护**

现在，您可以配置您的应用程序来检查 HTTP `Origin`头是否应该根据站点的来源进行检查，作为额外的 CSRF 防御。将配置中的以下内容设置为 true:

`config.action_controller.forgery_protection_origin_check = true`

**5.20.4 允许配置动作邮件队列名称**

默认的邮件队列名称是`mailers`。此配置选项允许您全局更改队列名称。在您的配置中设置以下内容:

`config.action_mailer.deliver_later_queue_name = :new_queue_name`

**5.20.5 支持动作邮件视图中的片段缓存**

在您的配置中设置`config.action_mailer.perform_caching`来决定您的动作邮件视图是否应该支持缓存。

`config.action_mailer.perform_caching = true`

**5.20.6 配置**的输出`db:structure:dump`

如果您使用的是`schema_search_path`或其他 PostgreSQL 扩展，您可以控制如何转储模式。设置为`:all`以生成所有转储，或者设置为`:schema_search_path`以从模式搜索路径生成。

`config.active_record.dump_schemas = :all`

**5.20.7 配置 SSL 选项以启用带有子域的 HSTS**

在配置中设置以下内容，以便在使用子域时启用 HSTS:

`config.ssl_options = { hsts: { subdomains: true } }`

**5.20.8 保存接收方的时区**

使用 Ruby 2.4 时，可以在调用`to_time`时保留接收者的时区。

`ActiveSupport.to_time_preserves_timezone = false`

## 5.21 JSON/JSONB 序列化的变化

在 Rails 5.0 中，JSON/JSONB 属性的序列化和反序列化方式发生了变化。现在，如果您设置一个等于`String`的列，活动记录将不再把该字符串转换成`Hash`，而是只返回该字符串。这不仅限于与模型交互的代码，还会影响到`db/schema.rb`中的`:default`列设置。建议您不要将列设置为等于一个`String`，而是传递一个`Hash`，它将自动与 JSON 字符串相互转换。

# 6 从 Rails 4.1 升级到 Rails 4.2

## 6.1 Web 控制台

首先，将`gem 'web-console', '~> 2.0'`添加到`Gemfile`中的`:development`组，并运行`bundle install`(升级 Rails 时不会包含它)。一旦安装完毕，您可以简单地将一个对控制台助手的引用(即`<%= console %>`)拖放到任何您想要启用它的视图中。您在开发环境中查看的任何错误页面上也会提供一个控制台。

## 6.2 响应者

`respond_with`和类级`respond_to`方法已经提取到`responders`宝石中。要使用它们，只需将`gem 'responders', '~> 2.0'`添加到您的`Gemfile`中。如果没有将`responders` gem 包含在您的依赖项中，对`respond_with`和`respond_to`(再次在类级别)的调用将不再有效:

`# app/controllers/users_controller.rb`

`class` `UsersController < ApplicationController`

`respond_to **:html**, **:json**`

`def`

`**@user**` `= User.find(params[**:id**])`

`respond_with **@user**`

`end`

`end`

**实例级** `**respond_to**` **不受影响，不需要额外的宝石:**

`# app/controllers/users_controller.rb`

`class` `UsersController < ApplicationController`

`def` `show`

`**@user**` `= User.find(params[**:id**])`

`respond_to do` `|format|`

`format.html`

`format.json { render json: **@user**`

`end`

`end`

`end`

## 6.3 事务回调中的错误处理

目前，活动记录抑制在`after_rollback`或`after_commit`回调中出现的错误，并仅将其打印到日志中。在下一个版本中，这些错误将不再被抑制。相反，错误将像在其他活动记录回调中一样正常传播。

当您定义一个`after_rollback`或`after_commit`回调时，您将收到一个关于这个即将到来的变化的反对警告。当您准备好时，您可以选择新的行为，并通过向您的`config/application.rb`添加以下配置来移除不赞成警告:

`config.active_record.raise_in_transactional_callbacks = true`

## 6.4 测试用例的排序

在 Rails 5.0 中，默认情况下，测试用例将随机执行。预见到这一变化，Rails 4.2 引入了一个新的配置选项`active_support.test_order`，用于显式指定测试顺序。这允许您通过将选项设置为`:sorted`来锁定当前行为，或者通过将选项设置为`:random`来选择未来行为。

如果不指定该选项的值，将发出不推荐使用的警告。为了避免这种情况，请将以下代码行添加到您的测试环境中:

`# config/environments/test.rb`

`Rails.application.configure do`

`config.active_support.test_order = **:sorted**` `# or `:random` if you prefer`

`end`

## 6.5 序列化属性

当使用自定义编码器(例如`serialize :metadata, JSON`)时，将`nil`赋给一个序列化属性会将其作为`NULL`保存到数据库中，而不是通过编码器传递`nil`值(例如当使用`JSON`编码器时为`"null"`)。

## 6.6 生产日志级别

在 Rails 5 中，生产环境的默认日志级别将改为`:debug`(从`:info`)。为了保留当前的缺省值，将下面一行添加到您的`production.rb`中:

`# Set to `:info` to match the current default, or set to `:debug` to opt-into`

`# the future default.`

`config.log_level = **:info**`

[](https://onlineitguru.com/blog/project-with-ruby-on-rails) [## 使用 Ruby on Rails 的项目| Ruby on Rails 博客| OnlineITGuru

### 特别是，你想为你的项目选择什么类型的语言。许多因素，例如，框架的价格，多少…

onlineitguru.com](https://onlineitguru.com/blog/project-with-ruby-on-rails) 

## 6.7 导轨模板中的`after_bundle`

如果您有一个在版本控制中添加所有文件的 Rails 模板，它将无法添加生成的 binstubs，因为它在 Bundler:

`# template.rb`

`generate(**:scaffold**, *"person name:string"*)`

`route *"root to: 'people#index'"*`

`rake(*"db:migrate"*)`

`git **:init**`

`git add: *"."*`

`git commit: %Q{ -m *'Initial commit'*` `}`

现在，您可以将`git`调用封装在一个`after_bundle`块中。它将在生成 binstubs 后运行。

`# template.rb`

`generate(**:scaffold**, *"person name:string"*)`

`route *"root to: 'people#index'"*`

`rake(*"db:migrate"*)`

`after_bundle do`

`git **:init**`

`git add: *"."*`

`git commit: %Q{ -m *'Initial commit'*` `}`

`end`

## 6.8 Rails HTML 杀毒软件

在你的应用程序中有一个净化 HTML 片段的新选择。古老的 html-scanner 方法现在正被官方弃用，取而代之的是`Rails HTML Sanitizer`。

这意味着方法`sanitize`、`sanitize_css`、`strip_tags`和`strip_links`得到了新实现的支持。

这种新型消毒剂内部使用丝瓜。丝瓜反过来使用 Nokogiri，它包装了用 C 和 Java 编写的 XML 解析器，所以无论运行哪个 Ruby 版本，清理都应该更快。

新版本更新了`sanitize`，所以可以用一个`Loofah::Scrubber`进行强大的刷洗。在这里看一些洗涤器的例子。

还增加了两个新的洗涤器:`PermitScrubber`和`TargetScrubber`。有关更多信息，请阅读 gem 的自述文件。

`PermitScrubber`和`TargetScrubber`的文档解释了如何完全控制何时以及如何去除元素。

如果您的应用程序需要使用旧的杀毒器实现，请在您的`Gemfile`中包含`rails-deprecated_sanitizer`:

`gem *'rails-deprecated_sanitizer'*`

## 6.9 Rails DOM 测试

`TagAssertions`模块(包含像`assert_tag`这样的方法)已经被弃用，取而代之的是来自`SelectorAssertions`模块的`assert_select`方法，该模块已经被提取到 rails-dom-testing gem 中。

## 6.10 掩蔽的真实性令牌

为了减少 SSL 攻击，`form_authenticity_token`现在被屏蔽了，因此它随每个请求而变化。因此，通过取消屏蔽然后解密来验证令牌。因此，任何验证来自依赖静态会话 CSRF 令牌的非 rails 表单的请求的策略都必须考虑到这一点。

## 6.11 行动邮件

以前，在 mailer 类上调用 mailer 方法会导致直接执行相应的实例方法。随着活动工单和`#deliver_later`的引入，这种情况不再存在。在 Rails 4.2 中，实例方法的调用被推迟，直到调用了`deliver_now`或`deliver_later`。例如:

`class` `Notifier < ActionMailer::Base`

`def` `notify(user, ...)`

`puts *"Called"*`

`mail(to: user.email, ...)`

`end`

`end`

`mail = Notifier.notify(user, ...) # Notifier#notify is not yet called at this point`

`mail = mail.deliver_now # Prints "Called"`

对于大多数应用程序来说，这不会导致任何明显的差异。但是，如果您需要同步执行一些非邮件程序方法，并且您以前依赖于同步代理行为，您应该直接将它们定义为邮件程序类的类方法:

`class`

`def`

`users.each`

`end`

`end`

## 6.12 外键支持

迁移 DSL 已经扩展到支持外键定义。如果你一直使用外国人宝石，你可能要考虑删除它。注意，Rails 的外键支持是外国人的子集。这意味着不是每个外国人的定义都可以被它的 Rails 迁移 DSL 对应物完全取代。

**迁移程序如下:**

1.  从`Gemfile`上拆下`gem "foreigner"`。
2.  运行`bundle install`。
3.  运行`bin/rake db:schema:dump`。
4.  确保`db/schema.rb`包含每个外键定义和必要的选项。

# 7 从 Rails 4.0 升级到 Rails 4.1

## 7.1 远程`<script>`标签的 CSRF 保护

或者，“我的测试失败了！！！?"或者“我的`<script>`小工具坏了！!"

跨站点请求伪造(CSRF)保护现在也包括带有 JavaScript 响应的 GET 请求。这可以防止第三方站点远程引用带有`<script>`标签的 JavaScript 来提取敏感数据。

这意味着您的功能和集成测试使用

`get **:index**, format: **:js**`

会触发 CSRF 保护。转到

`xhr **:get**, **:index**, format: **:js**`

显式测试一个`XmlHttpRequest`。

默认情况下，您自己的`<script>`标签也被视为跨源标签并被阻止。如果你真的想从`<script>`标签加载 JavaScript，你现在必须显式跳过对这些动作的 CSRF 保护。

## 7.2 弹簧

如果您想使用 Spring 作为您的应用程序预加载器，您需要:

1.  将`gem 'spring', group: :development`添加到你的`Gemfile`中。
2.  使用`bundle install`安装弹簧。
3.  用`bundle exec spring binstub --all`激活你的 binstubs。

默认情况下，用户定义的 rake 任务将在`development`环境中运行。如果你想让它们在其他环境下运行，请参考 Spring README 上的 [**Spring Boot 在线培训**](https://onlineitguru.com/spring-boot-training.html)

## 7.3 `config/secrets.yml`

如果您想使用新的`secrets.yml`约定来存储您的应用程序的秘密，您需要:

1.  在您的`config`文件夹中创建一个`secrets.yml`文件，内容如下:
2.  `development:`
3.  `secret_key_base:`
4.  `test:`
5.  `secret_key_base:`
6.  `production:`
7.  `secret_key_base: <%= ENV["SECRET_KEY_BASE"] %>`
8.  使用现有的`secret_token.rb`初始化器中的`secret_key_base`来为在生产模式下运行 Rails 应用程序的用户设置 SECRET_KEY_BASE 环境变量。或者，您可以简单地将现有的`secret_key_base`从`secret_token.rb`初始化器复制到`production`部分下的`secrets.yml`，替换为'<% = ENV[" SECRET _ KEY _ BASE "]%【T33 '。
9.  移除`secret_token.rb`初始化器。
10.  使用`rake secret`为`development`和`test`部分生成新的密钥。
11.  重新启动服务器。

## 7.4 对测试助手的更改

如果您的测试助手包含对`ActiveRecord::Migration.check_pending!`的调用，这可以被移除。现在，当您`require 'rails/test_help'`时，检查会自动完成，尽管在您的助手中留下这一行不会有任何危害。

## 7.5 Cookies 序列化程序

在 Rails 4.1 之前创建的应用程序使用`Marshal`将 cookie 值序列化到签名和加密的 cookie jars 中。如果您想在您的应用程序中使用新的基于`JSON`的格式，您可以添加一个包含以下内容的初始化文件:

`Rails.application.config.action_dispatch.cookies_serializer = **:hybrid**`

这将透明地将您现有的`Marshal`序列化 cookies 迁移到新的基于`JSON`的格式中。

当使用`:json`或`:hybrid`序列化器时，应该注意不是所有的 Ruby 对象都可以被序列化为 JSON。例如，`Date`和`Time`对象将被序列化为字符串，而`Hash` es 将把它们的键字符串化。

`class`

`def`

`cookies.encrypted[**:expiration_date**] = Date.tomorrow # => Thu, 20 Mar 2014`

`redirect_to action: *'read_cookie'*`

`end`

`def` `read_cookie`

`cookies.encrypted[**:expiration_date**] # => "2014-03-20"`

`end`

`end`

建议您只在 cookies 中存储简单的数据(字符串和数字)。如果必须存储复杂的对象，那么在后续请求中读取值时，需要手动处理转换。

如果您使用 cookie 会话存储，这也适用于`session`和`flash`散列。

## 7.6 闪存结构变化

Flash 消息键被规范化为字符串。仍然可以使用符号或字符串来访问它们。在闪存中循环总是会产生字符串密钥:

`flash[*"string"*] = *"a string"*`

`flash[**:symbol**] = *"a symbol"*`

`# Rails < 4.1`

`flash.keys # => ["string", :symbol]`

`# Rails >= 4.1`

`flash.keys # => ["string", "symbol"]`

确保将 Flash 消息键与字符串进行比较。

[](https://onlineitguru.com/blog/the-history-of-ruby-on-rails-controller) [## Ruby On Rails 控制器的历史

### 控制器负责内部动作和外部请求。它正在拥有。一个值得关注的选项…

onlineitguru.com](https://onlineitguru.com/blog/the-history-of-ruby-on-rails-controller) 

## 7.7 JSON 处理的变化

Rails 4.1 中有一些与 JSON 处理相关的重大变化。

**7.7.1 多 JSON 移除**

MultiJSON 已经走到了生命的尽头，已经从 Rails 中移除。

如果您的应用程序当前直接依赖于 MultiJSON，您有几个选择:

1.  将“multi_json”添加到您的`Gemfile`中。请注意，这在将来可能会停止工作
2.  使用`obj.to_json`和`JSON.parse(str)`从 MultiJSON 迁移。

不要简单地用`JSON.dump`和`JSON.load`替换`MultiJson.dump`和`MultiJson.load`。这些 JSON gem APIs 是用来序列化和反序列化任意 Ruby 对象的，通常是不安全的。

**7.7.2 JSON gem 兼容性**

历史上，Rails 与 JSON gem 有一些兼容性问题。在 Rails 应用程序中使用`JSON.generate`和`JSON.dump`可能会产生意想不到的错误。

Rails 4.1 通过将自己的编码器从 JSON gem 中分离出来，解决了这些问题。JSON gem APIs 将照常运行，但是它们不能访问任何 Rails 特有的特性。例如:

`class` `FooBar`

`def` `as_json(options = nil)`

`{ foo: *'bar'*` `}`

`end`

`end`

`>> FooBar.new.to_json # => "{\"foo\":\"bar\"}"`

`>> JSON.generate(FooBar.new, quirks_mode: true) # => "\"#<FooBar:0x007fa80a481610>\""`

**7.7.3 新的 JSON 编码器**

Rails 4.1 中的 JSON 编码器已经被重写，以利用 JSON gem。对于大多数应用程序来说，这应该是一个透明的变化。但是，作为重写的一部分，编码器中删除了以下功能:

1.  循环数据结构检测
2.  `encode_json`挂钩支架
3.  将`BigDecimal`对象编码为数字而不是字符串的选项

如果您的应用程序依赖于这些特性中的一个，您可以通过将`activesupport-json_encoder` gem 添加到您的`Gemfile`中来恢复它们。

**7.7.4 时间对象的 JSON 表示**

`#as_json`对于有时间成分的对象(`Time`、`DateTime`、`ActiveSupport::TimeWithZone`)，现在默认返回毫秒精度。如果您需要保留没有毫秒精度的旧行为，请在初始化器中设置以下内容:

`ActiveSupport::JSON::Encoding.time_precision = 0`

## 7.8 在内联回调块中使用`return`

以前，Rails 允许内联回调块以这种方式使用`return`:

`class`

`before_save { return` `false` `} # BAD`

`end`

这种行为从未得到有意的支持。由于`ActiveSupport::Callbacks`内部的变化，这在 Rails 4.1 中不再允许。在内联回调块中使用`return`语句会导致在执行回调时引发`LocalJumpError`。

使用`return`的内联回调块可以被重构以评估返回值:

`class` `ReadOnlyModel < ActiveRecord::Base`

`before_save { false` `} # GOOD`

`end`

或者，如果首选`return`，建议明确定义一种方法:

`class` `ReadOnlyModel < ActiveRecord::Base`

`before_save **:before_save_callback**`

`private`

`def` `before_save_callback`

`return` `false`

`end`

`end`

这一改变适用于 Rails 中使用回调的大多数地方，包括活动记录和活动模型回调，以及动作控制器中的过滤器(例如`before_action`)。

## 7.9 活动记录装置中定义的方法

Rails 4.1 在单独的上下文中评估每个 fixture 的 ERB，因此在 fixture 中定义的 helper 方法在其他 fixture 中不可用。

在多个 fixtures 中使用的 Helper 方法应该在新引入的`ActiveRecord::FixtureSet.context_class`中的模块上定义，在`test_helper.rb`中。

`module` `FixtureFileHelpers`

`def` `file_sha(path)`

`Digest::SHA2.hexdigest(File.read(Rails.root.join(*'test/fixtures'*, path)))`

`end`

`end`

`ActiveRecord::FixtureSet.context_class.include FixtureFileHelpers`

## 7.10 I18n 强制实施可用的区域设置

Rails 4.1 现在默认 I18n 选项`enforce_available_locales`到`true`。这意味着它将确保传递给它的所有语言环境都必须在`available_locales`列表中声明。

要禁用它(并允许 I18n 接受*任何* locale 选项),向您的应用程序添加以下配置:

`config.i18n.enforce_available_locales = false`

请注意，这个选项是作为一种安全措施添加的，以确保用户输入不能用作区域设置信息，除非它是预先已知的。因此，建议不要禁用此选项，除非您有很强的理由这样做。

## 7.11 在关系上调用的赋值函数方法

`Relation`不再有像`#map!`和`#delete_if`这样的变异方法。在使用这些方法之前，通过调用`#to_a`转换为`Array`。

它旨在防止在直接调用`Relation`上的 mutator 方法的代码中出现奇怪的错误和混乱。

`# Instead of this`

`Author.where(name: *'Hank Moody'*).compact!`

`# Now you have to do this`

`authors = Author.where(name: *'Hank Moody'*).to_a`

`authors.compact!`

## 7.12 默认范围的变更

默认范围不再被连锁条件覆盖。

在以前的版本中，当您在模型中定义一个`default_scope`时，它会被同一字段中的链式条件覆盖。现在它像任何其他作用域一样被合并。

**之前:**

`class` `User < ActiveRecord::Base`

`default_scope { where state: *'pending'*` `}`

`scope **:active**, -> { where state: *'active'*` `}`

`scope **:inactive**, -> { where state: *'inactive'*` `}`

`end`

`User.all`

`# SELECT "users".* FROM "users" WHERE "users"."state" = 'pending'`

`User.active`

`# SELECT "users".* FROM "users" WHERE "users"."state" = 'active'`

`User.where(state: *'inactive'*)`

`# SELECT "users".* FROM "users" WHERE "users"."state" = 'inactive'`

**之后:**

`class` `User < ActiveRecord::Base`

`default_scope { where state: *'pending'*`

`scope **:active**, -> { where state: *'active'*` `}`

`scope **:inactive**, -> { where state: *'inactive'*` `}`

`end`

`User.all`

`# SELECT "users".* FROM "users" WHERE "users"."state" = 'pending'`

`User.active`

`# SELECT "users".* FROM "users" WHERE "users"."state" = 'pending' AND "users"."state" = 'active'`

`User.where(state: *'inactive'*)`

`# SELECT "users".* FROM "users" WHERE "users"."state" = 'pending' AND "users"."state" = 'inactive'`

要获得之前的行为，需要使用`unscoped`、`unscope`、`rewhere`或`except`明确移除`default_scope`条件。

`class` `User < ActiveRecord::Base`

`default_scope { where state: *'pending'*`

`scope **:active**, -> { unscope(where: **:state**).where(state: *'active'*) }`

`scope **:inactive**, -> { rewhere state: *'inactive'*` `}`

`end`

`User.all`

`# SELECT "users".* FROM "users" WHERE "users"."state" = 'pending'`

`User.active`

`# SELECT "users".* FROM "users" WHERE "users"."state" = 'active'`

`User.inactive`

`# SELECT "users".* FROM "users" WHERE "users"."state" = 'inactive'`

## 7.13 从字符串呈现内容

Rails 4.1 为`render`引入了`:plain`、`:html`和`:body`选项。这些选项现在是呈现基于字符串的内容的首选方式，因为它允许您指定希望响应以哪种内容类型发送。

*   `render :plain`会将内容类型设置为`text/plain`
*   `render :html`会将内容类型设置为`text/html`
*   `render :body`将*而不是*设置内容类型头。

从安全的角度来看，如果你不希望在你的响应体中有任何标记，你应该使用`render :plain`,因为大多数浏览器会在你的响应中避开不安全的内容。

在未来的版本中，我们将不再使用`render :text`。所以请开始使用更精确的`:plain`、`:html`和`:body`选项。使用`render :text`可能会带来安全风险，因为内容是作为`text/html`发送的。

## 7.14 PostgreSQL json 和 hstore 数据类型

Rails 4.1 将把`json`和`hstore`列映射到一个字符串键红宝石`Hash`。在早期版本中，使用了一个`HashWithIndifferentAccess`。这意味着不再支持符号访问。基于`json`或`hstore`列顶部的`store_accessors`也是这种情况。确保一致地使用字符串键。

## 7.15】的显式块使用

Rails 4.1 现在希望在调用`ActiveSupport::Callbacks.set_callback`时传递一个显式块。这一变化源于 4.1 版本对`ActiveSupport::Callbacks`的大量重写。

`# Previously in Rails 4.0`

`set_callback **:save**, **:around**, ->(r, &block) { stuff; result = block.call; stuff }`

`# Now in Rails 4.1`

`set_callback **:save**, **:around**, ->(r, block) { stuff; result = block.call; stuff }`

# 8 从 Rails 3.2 升级到 Rails 4.0

如果您的应用程序当前使用的是早于 3.2.x 的任何版本的 Rails，那么您应该先升级到 Rails 3.2，然后再尝试升级到 Rails 4.0。

以下更改旨在将您的应用程序升级到 Rails 4.0。

## 8.1 HTTP 补丁

当在`config/routes.rb`中声明 RESTful 资源时，Rails 4 现在使用`PATCH`作为更新的主要 HTTP 动词。仍然使用`update`动作，并且`PUT`请求也将继续被路由到`update`动作。因此，如果您只使用标准的 RESTful 路由，则不需要做任何更改:

`resources **:users**`

`<%=` `form_for **@user**` `do` `|f| %>`

`class`

`def` `update`

`# No change needed; PATCH will be preferred, and PUT will still work.`

`end`

`end`

但是，如果您使用`form_for`来更新资源，并使用`PUT` HTTP 方法来定制路由，那么您将需要做出一个改变:

`resources **:users**, do`

`put **:update_name**, on: **:member**`

`end`

`<%=``form_for [ **:update_name**, **@user**``] do`

`class` `UsersController < ApplicationController`

`def`

`# Change needed; form_for will try to use a non-existent PATCH route.`

`end`

`end`

如果动作没有在公共 API 中使用，并且您可以随意更改 HTTP 方法，您可以更新您的路由以使用`patch`而不是`put`:

`PUT`对 Rails 4 中`/users/:id`的请求被路由到`update`，就像今天一样。所以，如果你有一个 API 来获得真正的 PUT 请求，它就会工作。路由器还将`PATCH`请求路由到`/users/:id`到`update`动作。

`resources **:users**`

`patch **:update_name**, on: **:member**`

`end`

如果动作正在公共 API 中使用，并且您不能更改为正在使用的 HTTP 方法，您可以更新您的表单以使用`PUT`方法:

`<%=`T30`], method: **:put**`

**8.1.1 关于媒体类型的说明**

`PATCH`动词的勘误表规定,`PATCH`应使用“diff”媒体类型。JSON 补丁就是这样一种格式。虽然 Rails 本身并不支持 JSON 补丁，但是添加支持还是很容易的:

`# in your controller`

`def update`

`respond_to do |format|`

`format.json do`

`# perform a partial update`

`@article.update params[:article]`

`end`

`format.json_patch do`

`# perform sophisticated change`

`end`

`end`

`end`

`# In config/initializers/json_patch.rb:`

`Mime::Type.register 'application/json-patch+json', :json_patch`

[](https://onlineitguru.com/blog/best-gems-for-ruby-on-rails) [## Ruby on Rails 最佳宝石| Ruby on Rails 博客

### 如果有人想创建现代应用程序。现在让我们来解释一下，Ruby on Rails 的最佳宝石。Ruby on Rails…

onlineitguru.com](https://onlineitguru.com/blog/best-gems-for-ruby-on-rails) 

由于 JSON Patch 最近才成为 RFC，所以还没有很多优秀的 Ruby 库。Aaron Patterson 的 hana 就是这样一个瑰宝，但是它并不完全支持规范中最后的一些变化。

## 8.2 Gemfile

Rails 4.0 从`Gemfile`中移除了`assets`组。升级时，您需要从您的`Gemfile`中删除该行。您还应该更新您的应用程序文件(在`config/application.rb`):

`# Require the gems listed in Gemfile, including any gems`

`# you've limited to :test, :development, or :production.`

`Bundler.require(*Rails.groups)`

## 8.3 供应商/插件

Rails 4.0 不再支持从`vendor/plugins`加载插件。你必须替换任何插件，把它们提取到 gems 并添加到你的`Gemfile`中。如果你选择不把它们变成宝石，你可以把它们移到，比如说，`lib/my_plugin/*`中，并在`config/initializers/my_plugin.rb`中添加一个合适的初始化器。

## 8.4 活动记录

*   由于关联的一些不一致性，Rails 4.0 已经从活动记录中删除了身份映射。如果您已经在您的应用程序中手动启用了它，您将不得不删除以下不再有效的配置:`config.active_record.identity_map`。
*   集合关联中的`delete`方法现在可以接收`Integer`或`String`参数作为记录 id，除了记录之外，非常类似于`destroy`方法。先前它提出了`ActiveRecord::AssociationTypeMismatch`这样的论点。从 Rails 4.0 开始，在删除记录之前，会自动尝试查找与给定 id 匹配的记录。
*   在 Rails 4.0 中，当列或表被重命名时，相关的索引也被重命名。如果您有重命名索引的迁移，则不再需要它们。
*   Rails 4.0 仅将`serialized_attributes`和`attr_readonly`改为类方法。你不应该使用实例方法，因为它现在已经过时了。您应该将它们改为使用类方法，例如`self.serialized_attributes`到`self.class.serialized_attributes`。
*   当使用默认的编码器时，将`nil`赋给一个序列化的属性会将它作为`NULL`保存到数据库中，而不是通过 YAML ( `"--- \n...\n"`)传递`nil`值。
*   Rails 4.0 移除了`attr_accessible`和`attr_protected`特性，支持强参数。您可以使用受保护的属性 gem 来顺利升级。
*   如果您没有使用受保护的属性，您可以移除任何与此 gem 相关的选项，如`whitelist_attributes`或`mass_assignment_sanitizer`选项。
*   Rails 4.0 要求作用域使用可调用的对象，比如 Proc 或 lambda:

`scope **:active**, where(active: true)`

`# becomes`

`scope **:active**, -> { where active: true` `}`

*   Rails 4.0 已经弃用了`ActiveRecord::Fixtures`而支持`ActiveRecord::FixtureSet`。
*   Rails 4.0 已经弃用了`ActiveRecord::TestCase`而支持`ActiveSupport::TestCase`。
*   Rails 4.0 已经摒弃了旧式的基于哈希的 finder API。这意味着以前接受“查找选项”的方法不再接受。例如，`Book.find(:all, conditions: { name: '1984' })`已被弃用，取而代之的是`Book.where(name: '1984')`
*   除了`find_by_...`和`find_by_...!`之外的所有动态方法都被否决。以下是你应对这些变化的方法:
*   `find_all_by_...`变成了`where(...)`。
*   `find_last_by_...`变成了`where(...).last`。
*   `scoped_by_...`变成了`where(...)`。
*   `find_or_initialize_by_...`变成了`find_or_initialize_by(...)`。
*   `find_or_create_by_...`变成了`find_or_create_by(...)`。
*   注意`where(...)`返回一个关系，而不是像旧的查找器那样返回一个数组。如果你需要一个`Array`，使用`where(...).to_a`。
*   这些等效的方法可能不会执行与前面的实现相同的 SQL。
*   要重新启用旧的查找器，可以使用 active record-deprecated _ finders gem。
*   Rails 4.0 已经更改为`has_and_belongs_to_many`关系的默认连接表，去掉了第二个表名的公共前缀。具有共同前缀的模型之间的任何现有的`has_and_belongs_to_many`关系必须用`join_table`选项指定。例如:

`CatalogCategory < ActiveRecord::Base`

`has_and_belongs_to_many **:catalog_products**, join_table: *'catalog_categories_catalog_products'*`

`end`

`CatalogProduct < ActiveRecord::Base`

`has_and_belongs_to_many **:catalog_categories**, join_table: *'catalog_categories_catalog_products'*`

`end`

*   注意前缀也考虑了范围，所以`Catalog::Category`和`Catalog::Product`或者`Catalog::Category`和`CatalogProduct`之间的关系需要类似地更新。

## 8.5 活动资源

Rails 4.0 将活动资源提取到自己的 gem 中。如果你仍然需要这个特性，你可以在你的`Gemfile`中添加活动的资源宝石。

## 8.6 活动模型

*   Rails 4.0 改变了错误与`ActiveModel::Validations::ConfirmationValidator`的关联方式。现在，当确认验证失败时，错误将被附加到`:#{attribute}_confirmation`而不是`attribute`。
*   Rails 4.0 已经把`ActiveModel::Serializers::JSON.include_root_in_json`默认值改成了`false`。现在，活动模型序列化程序和活动记录对象具有相同的默认行为。这意味着您可以在`config/initializers/wrap_parameters.rb`文件中注释或删除以下选项:

`# Disable root element in JSON by default.`

`# ActiveSupport.on_load(:active_record) do`

`# self.include_root_in_json = false`

`# end`

## 8.7 行动包

*   Rails 4.0 引入了`ActiveSupport::KeyGenerator`，并以此为基础生成和验证签名的 cookies(以及其他内容)。如果您保留现有的`secret_token`并添加新的`secret_key_base`，那么用 Rails 3.x 生成的现有签名 cookies 将被透明地升级。

`# config/initializers/secret_token.rb`

`Myapp::Application.config.secret_token = *'existing secret token'*`

`Myapp::Application.config.secret_key_base = *'new secret key base'*`

请注意，您应该等到 Rails 4.x 上有了 100%的用户群，并且有理由确信您不需要回滚到 Rails 3.x 时再设置`secret_key_base`，这是因为基于 Rails 4.x 中的新`secret_key_base`签名的 cookies 与 Rails 3.x 并不向后兼容，您可以保留现有的`secret_token`，不设置新的`secret_key_base`，并忽略弃用警告，直到您有理由确信您的升级已经完成。

如果您依赖外部应用程序或 JavaScript 来读取您的 Rails 应用程序的签名会话 cookie(或一般的签名 cookie ),那么您不应该设置`secret_key_base`,直到您解除了这些顾虑。

*   如果设置了`secret_key_base`，Rails 4.0 会对基于 cookie 的会话内容进行加密。Rails 3.x 对基于 cookie 的会话内容进行了签名，但没有加密。签名的 cookies 是“安全的”,因为它们被验证是由您的应用程序生成的，并且是防篡改的。但是，最终用户可以查看这些内容，对内容进行加密消除了这种警告/顾虑，而不会显著降低性能。

请阅读 Pull Request #9978，了解有关移动到加密会话 cookies 的详细信息。

*   Rails 4.0 去掉了`ActionController::Base.asset_path`选项。使用资产管道功能。
*   Rails 4.0 已经弃用了`ActionController::Base.page_cache_extension`选项。用`ActionController::Base.default_static_extension`代替。
*   Rails 4.0 已经从 Action Pack 中移除了动作和页面缓存。你需要添加`actionpack-action_caching`宝石以便在你的控制器中使用`caches_action`和`actionpack-page_caching`来使用`caches_page`。
*   Rails 4.0 移除了 XML 参数解析器。如果您需要此功能，您将需要添加`actionpack-xml_parser`宝石。
*   Rails 4.0 使用返回 nil 的符号或过程来改变默认的`layout`查找集。要获得“无布局”行为，请返回 false 而不是 nil。
*   Rails 4.0 将默认的 memcached 客户端从`memcache-client`改为`dalli`。要升级，只需将`gem 'dalli'`添加到您的`Gemfile`中。
*   Rails 4.0 反对控制器中的`dom_id`和`dom_class`方法(在视图中它们很好)。您需要在需要此功能的控制器中包含`ActionView::RecordIdentifier`模块。
*   Rails 4.0 不支持`link_to`助手的`:confirm`选项。你应该依靠一个数据属性(例如`data: { confirm: 'Are you sure?' }`)。这种贬低也关系到基于这个的帮手(比如`link_to_if`或者`link_to_unless`)。

[](https://onlineitguru.com/blog/role-of-cucumber-in-ruby-on-rails) [## Cucumber 在 Ruby on Rails 中的作用| Ruby On Rails 博客| OnlineITGuru

### 我希望你们这些人看了标题后认为作者疯了。如果你这样想，那你就错了。此外…

onlineitguru.com](https://onlineitguru.com/blog/role-of-cucumber-in-ruby-on-rails) 

*   Rails 4.0 改变了`assert_generates`、`assert_recognizes`和`assert_routing`的工作方式。现在所有这些断言都提出了`Assertion`而不是`ActionController::RoutingError`。
*   如果定义了冲突的命名路由，Rails 4.0 会引发一个`ArgumentError`。这可以由显式定义的命名路由或由`resources`方法触发。这里有两个与名为`example_path`的路由冲突的例子:

`get *'one'*`

`get *'two'*`

`resources **:examples**`

`get *'clashing/:id'*` `=> *'test#example'*, as: **:example**`

在第一种情况下，您可以简单地避免对多个路由使用相同的名称。在第二种情况下，您可以使用`resources`方法提供的`only`或`except`选项来限制创建的路径，详见路径指南。

*   Rails 4.0 还改变了 unicode 字符路线的绘制方式。现在可以直接画 unicode 字符路线了。如果您已经绘制了这样的路线，您必须更改它们，例如:

`get Rack::Utils.escape(*'こんにちは'*), controller: *'welcome'*, action: *'index'*`

成为

`get *'こんにちは'*, controller: *'welcome'*, action: *'index'*`

*   Rails 4.0 要求使用`match`的路由必须指定请求方法。例如:

`# Rails 3.x`

`match *'/'*` `=> *'root#index'*`

`# becomes`

`match *'/'*` `=> *'root#index'*, via: **:get**`

`# or`

`get *'/'*`

*   Rails 4.0 移除了`ActionDispatch::BestStandardsSupport`中间件，`<!DOCTYPE html>`已经按照 https://msdn . Microsoft . com/en-us/library/jj 676915(v = vs . 85)触发了标准模式。aspx 和 ChromeFrame 头被移到了`config.action_dispatch.default_headers`。

请记住，您还必须从应用程序代码中删除对中间件的任何引用，例如:

`# Raise exception`

`config.middleware.insert_before(Rack::Lock, ActionDispatch::BestStandardsSupport)`

此外，检查`config.action_dispatch.best_standards_support`的环境设置，如果有，将其删除。

*   Rails 4.0 允许通过设置`config.action_dispatch.default_headers`来配置 HTTP 头。默认值如下:

`config.action_dispatch.default_headers = {`

`*'X-Frame-Options'*`

`*'X-XSS-Protection'*`

`}`

请注意，如果您的应用程序依赖于在`<frame>`或`<iframe>`中加载某些页面，那么您可能需要显式地将`X-Frame-Options`设置为`ALLOW-FROM ...`或`ALLOWALL`。

*   在 Rails 4.0 中，预编译资产不再自动从`vendor/assets`和`lib/assets`复制非 JS/CSS 资产。Rails 应用程序和引擎开发人员应该将这些资产放入`app/assets`或配置`config.assets.precompile`。
*   在 Rails 4.0 中，当动作不处理请求格式时，`ActionController::UnknownFormat`被引发。默认情况下，通过响应 406 不可接受来处理异常，但是您现在可以覆盖它。在 Rails 3 中，总是返回不可接受的 406。没有覆盖。
*   在 Rails 4.0 中，当`ParamsParser`无法解析请求参数时，会引发一个通用的`ActionDispatch::ParamsParser::ParseError`异常。例如，您可能想要拯救这个异常，而不是低级别的`MultiJson::DecodeError`。
*   在 Rails 4.0 中，当引擎被安装在一个由 URL 前缀提供服务的应用程序上时，`SCRIPT_NAME`被正确地嵌套。您不再需要设置`default_url_options[:script_name]`来解决被覆盖的 URL 前缀。
*   Rails 4.0 弃用了`ActionController::Integration`而支持`ActionDispatch::Integration`。
*   Rails 4.0 弃用了`ActionController::IntegrationTest`而支持`ActionDispatch::IntegrationTest`。
*   Rails 4.0 弃用了`ActionController::PerformanceTest`而支持`ActionDispatch::PerformanceTest`。
*   Rails 4.0 弃用了`ActionController::AbstractRequest`，转而支持`ActionDispatch::Request`。
*   Rails 4.0 弃用了`ActionController::Request`而支持`ActionDispatch::Request`。
*   Rails 4.0 弃用了`ActionController::AbstractResponse`而支持`ActionDispatch::Response`。
*   Rails 4.0 弃用了`ActionController::Response`而支持`ActionDispatch::Response`。
*   Rails 4.0 弃用了`ActionController::Routing`而支持`ActionDispatch::Routing`。

## 8.8 积极支持

Rails 4.0 移除了`ERB::Util#json_escape`的别名`j`，因为`j`已经被用于`ActionView::Helpers::JavaScriptHelper#escape_javascript`。

高速缓存

Rails 3.x 和 4.0 之间的缓存方法发生了变化。您应该更改缓存命名空间并推出冷缓存。

## 8.9 助手装载顺序

在 Rails 4.0 中，来自多个目录的助手的加载顺序发生了变化。以前，它们被收集起来，然后按字母顺序排列。升级到 Rails 4.0 后，helpers 将保留加载目录的顺序，并且在每个目录中只按字母顺序排序。除非您显式地使用`helpers_path`参数，否则这个变化只会影响从引擎加载助手的方式。如果您依赖订购，您应该检查升级后是否有正确的方法可用。如果您想改变发动机的装载顺序，您可以使用`config.railties_order=`方法。

## 8.10 主动记录观察器和动作控制器清扫器

`ActiveRecord::Observer`和`ActionController::Caching::Sweeper`已经被提取到`rails-observers`宝石中。如果你需要这些功能，你将需要添加`rails-observers`宝石。

## 8.11 链轮-轨道

*   `assets:precompile:primary`和`assets:precompile:all`已被删除。用`assets:precompile`代替。
*   `config.assets.compress`选项应改为`config.assets.js_compressor`，例如:

`config.assets.js_compressor = **:uglifier**`

## 8.12 不锈钢栏杆

*   `asset-url`带有两个参数，不推荐使用。比如:`asset-url("rails.png", image)`变成`asset-url("rails.png")`。

# 9 从 Rails 3.1 升级到 Rails 3.2

如果您的应用程序当前使用的是早于 3.1.x 的任何版本的 Rails，您应该在尝试更新到 Rails 3.2 之前升级到 Rails 3.1。

以下更改旨在将您的应用程序升级到最新的 Rails 3.2 . x 版本。

## 9.1 Gemfile

对您的`Gemfile`进行以下更改。

`gem *'rails'*, *'3.2.21'*`

`group **:assets**` `do`

`gem *'sass-rails'*, *'~> 3.2.6'*`

`gem *'coffee-rails'*, *'~> 3.2.2'*`

`gem *'uglifier'*, *'>= 1.0.3'*`

`end`

[](https://onlineitguru.com/blog/why-we-use-ruby-on-rails-for-web-applications) [## 为什么我们在 web 应用中使用 Ruby on Rails

### Rails 是 Ruby 的第一个 web 框架，Ruby 编程语言设计用于 rails web 应用程序…

onlineitguru.com](https://onlineitguru.com/blog/why-we-use-ruby-on-rails-for-web-applications) 

## 9.2 配置/环境/开发. rb

有几个新的配置设置应该添加到开发环境中:

`# Raise exception on mass assignment protection for Active Record models`

`config.active_record.mass_assignment_sanitizer = **:strict**`

`# Log the query plan for queries taking more than this (works`

`# with SQLite, MySQL, and PostgreSQL)`

`config.active_record.auto_explain_threshold_in_seconds = 0.5`

## 9.3 配置/环境/测试. rb

`mass_assignment_sanitizer`配置设置也应添加到`config/environments/test.rb`:

`# Raise exception on mass assignment protection for Active Record models`

`config.active_record.mass_assignment_sanitizer = **:strict**`

## 9.4 供应商/插件

Rails 3.2 反对`vendor/plugins`，Rails 4.0 将彻底移除它们。虽然这并不是 Rails 3.2 升级的必要部分，但是您可以通过将插件提取到 gems 并添加到您的`Gemfile`中来开始替换任何插件。如果你选择不把它们变成宝石，你可以把它们移到，比如说，`lib/my_plugin/*`中，并在`config/initializers/my_plugin.rb`中添加一个合适的初始化器。

## 9.5 活动记录

选项`:dependent => :restrict`已从`belongs_to`中移除。如果您想防止在存在任何关联对象的情况下删除该对象，您可以设置`:dependent => :destroy`并在从任何关联对象的销毁回调中检查关联的存在之后返回`false`。

# 10 从 Rails 3.0 升级到 Rails 3.1

如果您的应用程序当前使用的是早于 3.0.x 的任何版本的 Rails，您应该在尝试更新到 Rails 3.1 之前升级到 Rails 3.0。

以下更改旨在将您的应用程序升级到 Rails 3.1.12，这是 Rails 的最新 3.1.x 版本。

## 10.1 Gemfile

对您的`Gemfile`进行以下更改。

`gem *'rails'*, *'3.1.12'*`

`gem *'mysql2'*`

`# Needed for the new asset pipeline`

`group **:assets**` `do`

`gem *'sass-rails'*, *'~> 3.1.7'*`

`gem *'coffee-rails'*, *'~> 3.1.1'*`

`gem *'uglifier'*, *'>= 1.0.3'*`

`end`

`# jQuery is the default JavaScript library in Rails 3.1`

`gem *'jquery-rails'*`

## 10.2 配置/应用程序. rb

资产管道需要增加以下内容:

`config.assets.enabled = true`

`config.assets.version = *'1.0'*`

如果您的应用程序对资源使用“/assets”路由，您可能希望更改用于资产的前缀以避免冲突:

`# Defaults to '/assets'`

`config.assets.prefix = *'/asset-files'*`

## 10.3 配置/环境/开发. rb

移除 RJS 设置`config.action_view.debug_rjs = true`。

如果启用了资产管道，请添加这些设置:

`# Do not compress assets`

`config.assets.compress = false`

`# Expands the lines which load the assets`

`config.assets.debug = true`

## 10.4 配置/环境/生产. rb

同样，下面的大部分变化是针对资产管道的。您可以在《资产管道指南》中了解更多相关信息。

`# Compress JavaScripts and CSS`

`config.assets.compress = true`

`# Don't fallback to assets pipeline if a precompiled asset is missed`

`config.assets.compile = false`

`# Generate digests for assets URLs`

`config.assets.digest = true`

`# Defaults to Rails.root.join("public/assets")`

`# config.assets.manifest = YOUR_PATH`

`# Precompile additional assets (application.js, application.css, and all non-JS/CSS are already added)`

`# config.assets.precompile += %w( admin.js admin.css )`

`# Force all access to the app over SSL, use Strict-Transport-Security, and use secure cookies.`

`# config.force_ssl = true`

## 10.5 配置/环境/测试. rb

您可以在测试环境中添加以下内容来帮助测试性能:

`# Configure static asset server for tests with Cache-Control for performance`

`config.public_file_server.enabled = true`

`config.public_file_server.headers = {`

`*'Cache-Control'*` `=> *'public, max-age=3600'*`

`}`

## 10.6 config/initializer/wrap _ parameters . Rb

如果您希望将参数包装到嵌套散列中，请添加包含以下内容的文件。在新的应用程序中，这是默认打开的。

`# Be sure to restart your server when you modify this file.`

`# This file contains settings for ActionController::ParamsWrapper which`

`# is enabled by default.`

`# Enable parameter wrapping for JSON. You can disable this by setting :format to an empty array.`

`ActiveSupport.on_load(**:action_controller**) do`

`wrap_parameters format: [**:json**]`

`end`

`# Disable root element in JSON by default.`

`ActiveSupport.on_load(**:active_record**) do`

`self.include_root_in_json = false`

`end`

## 10.7 config/initializer/session _ store . Rb

您需要将会话密钥更改为新的密钥，或者删除所有会话:

`# in config/initializers/session_store.rb`

`AppName::Application.config.session_store **:cookie_store**, key: *'SOMETHINGNEW'*`

**或**

`$ bin/rake db:sessions:clear`

## 10.8 视图中资产辅助对象引用中的 Remove :cache 和:concat 选项

*   对于资产管道，不再使用:cache 和:concat 选项，请从视图中删除这些选项。**