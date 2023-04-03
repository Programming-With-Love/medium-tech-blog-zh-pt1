# Ruby On Rails 开发人员的 10 个有用技巧

> 原文：<https://medium.com/quick-code/10-useful-tips-for-ruby-on-rails-developers-56f95fe1654d?source=collection_archive---------6----------------------->

Rails 是用 Ruby 编程语言编写的模型-视图-控制器 Web 框架。它最大的吸引力之一是能够快速制作基于 CRUD 的 Web 应用程序。Rails 相对于其他框架的一个很大的优势是它重视约定胜于配置。如果您遵循正确的惯例，您可以避免冗长的文件配置，事情就可以正常工作了！因此，您可以花更少的时间编写枯燥的配置文件，花更多的时间关注业务逻辑。

![](img/0cd032e05b2c1bf80cf5d57aa045aec5.png)

在下面的概述中，我们为 Ruby on Rails 开发者(包括新手和专业人士)提供了 **10 个有用的技巧、想法和资源。请在这篇文章的评论中分享你的技巧、想法和建议！**

如果你想深入了解 Ruby On Rails，请点击此链接 [**Ruby On Rails 培训**](https://onlineitguru.com/ruby-on-rails-online-training-placement.html)

# 1.插件节省时间

Rails 有一个定义良好的插件结构，使您能够轻松地在应用程序中安装和使用插件。Rails 之父 David Heinemeier Hansson 曾经说过，他在每个 Rails 应用程序中使用五到六个插件。

有一个古老的开发人员智慧金块“最好的代码是根本没有代码。”在 Rails 中进行开发时，让我们如此高效的部分原因是，我们不需要编写所有代码，因为社区中的其他人已经编写了一个插件，提供了我们需要的功能。

在 Rails 中安装插件有几种方法，但是最常用的是使用脚本:

```
# Install from a git repo
script/plugin install git://github.com/mislav/will_paginate.git# Install from a url
script/plugin install http://topfunky.net/svn/plugins/calendar_helper
```

你可以通过擅长搜索网页(尤其是万能的 GitHub)来节省大量的时间和精力。找到插件的几个地方是 Core Rails、Railsify 和 Rails 插件目录。需要与现有的 API 集成还是使用某种标准格式的数据？需要标记、分页或其他常见的 Web 应用程序功能吗？有可能一些优秀的 Rails 或 Ruby 开发人员已经有了一个项目，可以让你至少完成大部分工作。

# 2.使用 Rspec 进行测试既有趣又简单

对大多数人来说，“考试”这个词会让他们想起学校考试的可怕记忆。然而，当使用 Rails 时，**自动化测试**可以让您的开发体验更加愉快。虽然许多人对他们有强烈的，近乎宗教的看法，但从本质上来说，自动化测试只是你编写的小助手程序，它运行你的主代码来确保它们做正确的事情。如果做得好，测试将会改善你的工作流程，增加你对结果的信心。

Rails 自带了一个测试框架，但是在过去的几年里，所有优秀的孩子都在使用一个叫做 Rspec 的替代品。Rspec 最大的优势是它指定测试的语法:

```
describe "My Cool library" do before do
    @cool = Cool.new end it "should be full of penguins." do
    @cool.get_penguins!
    @cool.penguin_count.should == 10
  end it "should melt if it gets too warm"
end
```

Rspec 语法的伟大之处在于它使用了多少英语。describe 块设置上下文和其中的每个断言，它接受用于解释代码应该做什么的字符串。通常，这是最重要的阶段:你坐下来写断言，尽你所能，然后你想，“对，这段代码实际上应该做什么？”

因为 Rspec 允许您省略实现断言的块(如在第二个 melt 示例中)，所以您可以快速地对所有功能进行头脑风暴，然后在编写代码时回过头来实现测试。与此同时，Rspec 会将这些测试视为“待定”，并在您的测试运行中给你一些提示。

除了首先帮助您编写代码之外，测试的另一个好处是，一旦您有了足够多的测试，它们会让您看到所有代码是如何相关的，从而很容易知道您最近的更改是否破坏了应用程序中的其他内容。 **Rspec 通过使用定制的生成器来创建测试和你的代码的剩余部分，使得获得良好的测试覆盖率变得很容易:**

```
$ script/generate rpsec_scaffold MyModel
```

一旦您进行了测试以确保基本功能成功运行，您就可以放心地进行更改和添加新代码，而不必担心引入不可见的错误。只要你定期运行你的测试，一旦你破坏了什么，你就会知道。正如大兵哥教我们的，知道是成功的一半！

# 3.节省时间，使用耙子

项目通常不仅仅包括特定于应用程序的代码。必须创建样本数据、查询 Web 服务、移动文件、重写代码片段等等。抵制外壳脚本或塞进迁移或控制器的冲动。使用耙子。太棒了。

Rake 是一个用 Ruby 写的构建工具，和 make 非常相似。Rails 项目已经定义了几个 Rake 任务；要查看这些，运行 rake -T 命令。

```
macbook$ rake -Trake data:bootstrap     # load in some basic data [caution: will nuke and replcace cate...
rake db:create:all      # Create all the local databases defined in config/database.yml
rake db:drop         # Drops the database for the current RAILS_ENV
...
rake ts:run          # Stop if running, then start a Sphinx searchd daemon using Thi...
rake ts:start        # Start a Sphinx searchd daemon using Thinking Sphinx's settings
rake ts:stop         # Stop Sphinx using Thinking Sphinx's settings
```

添加你自己的 Rake 任务非常容易。在下面的例子中，您可以看到任务是有名称空间的，有描述和任务名称，允许您用 Ruby 编写。

```
namespace :data do desc "load in some basic data [caution: will nuke and replcace categories, categorizations and inventory items]"
  task :bootstrap => :environment do
    # clean out existing:
    [Category, Categorization, InventoryItem].each{|m| m.find(:all).each{|i| i.destroy}}
    InventoryItem.create! :name => "Compass"
    c = Category.create! :name => "Basic Apparel" ["Men’s Heavyweight Cotton T",
    "Men’s Heavyweight Polo",
    "Women’s Organic Cotton Fitted T",
    "Women’s Fitted Polo",
    "Children’s T-Shirt",
    "Jr’s Distressed Hoodie",
    "Hemp Messenger Bag"].each do |name|
      c.inventory_items.create! :name => name
    end ...end
```

# 4.跟踪应用程序异常

例外发生了，当它们发生时，你想知道它们！你的客户不应该是那个告诉你问题已经发生的人；您应该已经意识到了这个问题，并正在努力解决它。Rails 中提供异常通知已经有一段时间了。有一些异常通知插件，可以很容易得到通知。然而，一些服务，如 Airbrake Bug Tracker 和 Get Exceptional，为您的应用程序增加了很多价值。

这两个服务都易于安装，并提供了一个很好的 UI 来跟踪您的异常。你甚至可以注册一个免费账户来尝试一些东西。

通过**集中应用程序异常**，你可以看到这些异常发生的频率，它们发生在什么环境中(特定的浏览器？一个特定的位置？)，存在哪些参数以及完整的堆栈跟踪。这种数据集中化有助于您发现模式并更快地解决问题，从而带来更好的应用程序和更满意的用户。

# 5.框架和服务器与机架上的导轨混合搭配

从版本 2.3 开始，Rails 在机架顶部运行。Rack 使得 Ruby Web 框架和服务器之间的混合和匹配成为可能。如果你使用的是支持它的框架(比如 Rails、Sinatra、Camping 等)，你可以选择任何支持它的服务器(Mongrel、Thin、Phusion Passenger 等)。)，反之亦然。

除了引入各种新的部署选项，这一变化还意味着 Rails 现在可以进入令人兴奋的机架中间件世界。因为 Rack 位于您的应用程序和服务器的交叉点，所以它可以直接提供各种常用功能。Rack::Cache 就是一个很好的例子。

Rack::Cache 为您的应用程序提供了一个**缓存层**，您只需在响应中发送正确的头就可以控制它。换句话说，您所要做的就是在机架配置文件中安装一些代码:

```
require 'rack/cache'use Rack::Cache,
  :metastore => 'file:/tmp/rack_meta_dir',
  :entitystore => 'file:/tmp/rack_body_dir',
  :verbose => true
```

并确保您的控制器动作发送正确的头(例如，通过设置`request.headers[“Cache-Control”]`)和 Bam！，你有缓存。

# 6.轻松的数据转储

您经常需要将数据从生产环境转移到开发环境，或者从开发环境转移到您的本地，或者从您的本地转移到另一个开发人员的本地。我们反复使用的一个插件是 Yaml_db。这个漂亮的小插件使您能够通过发出 Rake 命令来**转储或加载数据。数据保存在 db/data.yml 中的一个 yaml 文件中，如果您需要检查数据，这是非常容易移植和阅读的。**

```
rake db:data:dumpexample data found in db/data.yml---
campaigns:
  columns:
  - id
  - client_id
  - name
  - created_at
  - updated_at
  - token records:
  - - "1"
    - "1"
    - First push
    - 2008-11-03 18:23:53
    - 2008-11-03 18:23:53
    - 3f2523f6a665
  - - "2"
    - "2"
    - First push
    - 2008-11-03 18:26:57
    - 2008-11-03 18:26:57
    - 9ee8bc427d94
```

# 7.把你的常数放在一个地方

所有的应用程序都有常量和变量，它们是用不变的数据定义的，比如应用程序的名称、标语、关键选项的值等等。我们使用 **Rails 初始化器特性**来定义一个`config/initializers/site_config.rb`来存放这些常量。通过使用这个约定，项目中的所有开发人员都知道在哪里可以找到这些常量，并且可以快速地做出更改。

许多人对何时将常量放在`site_config.rb`中而不是放在使用它的类中有疑问。对于只在一个类中使用的常量，我们建议将其放在那个类中。但是，如果常量在多个位置使用，就把它放在 site_config.rb 中。

```
# File config/initializers/site_config.rbREPORT_RECIPIENT = 'jenn@scg.com'
REPORT_ADMIN = 'michael@scg.com'
```

# 8.用于处理代码的控制台

有时你可能对一些代码感到好奇。这样行得通吗？产量是多少？我能改变什么？Rails 附带了一个很棒的工具，叫做控制台。通过运行脚本/控制台，你将进入一个**交互环境，在这里你可以访问你的 Rails 代码**，就像应用程序正在运行一样。

这种环境非常有帮助。它经常在生产环境中使用，以便在不登录数据库的情况下快速查看数据。要在生产环境中实现这一点，请使用脚本/控制台 RAILS_ENV=production:

```
macbook$ ./script/console
Loading development environment (Rails 2.1.1)
>> a = Album.find(:first)
=> #
>>
```

# 9.丑陋的景色让你沮丧？试试 Haml。

视图是 Rails 应用程序生成访问者实际看到和使用的 HTML 页面的方式。默认情况下，Rails 使用 ERb 模板系统允许您在标记中嵌入 Ruby，这样您就可以根据需要插入数据。然而，Rails 的最新版本允许你选择模板语言，现在 Ruby 网络上已经在热烈讨论一个叫做 Haml 的替代系统。

Haml 是作为“标记俳句”进行营销的。它的 **CSS 启发的语法让你专注于数据的语义**，而不是担心使用 ERb 和 HTML 带来的所有尖括号。

作为比较，这里是标准 ERb 中的一点标记:

```
<%= print_date %>
<%= current_user.address %><%= current_user.email %>
<%= h current_user.bio %>
```

这是 Haml 中的等价形式:

```
#profile
  .left.column
    #date= print_date
    #address= current_user.address
  .right_column
    #email= current_user.email
    #bio= h(current_user.bio)
```

Haml 并不适合所有人，许多人会发现这种语法很奇怪。但是如果你重视简洁并且精通 CSS，Haml 可能是你的不二之选。

# 10.知道在哪里可以看到 Rails 中发生的事情

Rails 和 Ruby 都有**大而活跃的社区**，不断产生变化、改进和新项目。试图跟上所有的活动可能是令人畏惧的，但是如果你想从社区的最佳工作中受益并继续增加你的 Rails 和 Ruby 知识，这是必不可少的。