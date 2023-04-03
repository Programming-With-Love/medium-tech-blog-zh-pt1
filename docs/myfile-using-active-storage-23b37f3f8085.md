# 我的文件:使用活动存储

> 原文：<https://medium.com/oracledevs/myfile-using-active-storage-23b37f3f8085?source=collection_archive---------4----------------------->

![](img/5c8303612667e569d7cc0950b4518b5b.png)

Photo by [**Cleyder Duque**](https://www.pexels.com/@cleyder-duque-1585619?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) from [**Pexels**](https://www.pexels.com/photo/photo-of-warehouse-3821385/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)

# 介绍

本文基于我们在第一篇文章的[中构建的简单的 *myfile* 示例应用程序。我们将切换到使用活动存储来处理文件上传/下载，将所有文件存储在 OCI 对象存储中。](/oracledevs/introducing-myfile-a-base-rails-app-on-oci-a91149812cbc)

# 背景

这是关于在 OCI 中使用一些流行的红宝石的系列文章的一部分。如果你没有跟上，请参见[的第一篇文章](/oracledevs/introducing-myfile-a-base-rails-app-on-oci-a91149812cbc)，它提供了我们将在本教程中使用的基础应用。

我们将与 [OCI 对象存储](https://docs.oracle.com/en-us/iaas/Content/Object/Concepts/objectstorageoverview.htm)合作，所以如果你还没有 OCI 账户，你应该[现在就注册](https://www.oracle.com/cloud/free/#always-free?source=:ex:tb:::::WWMK220126P00003&SC=:ex:tb:::::WWMK220126P00003&pcode=WWMK220126P00003)一个！有任何问题、评论或想与其他志同道合的技术极客聊天吗？请随意评论这篇文章(如下)以及加入 [Slack](https://join.slack.com/t/oracledevrel/shared_invite/zt-uffjmwh3-ksmv2ii9YxSkc6IpbokL1g) 的乐趣。

# 事实真相

实际上，我们不会引入任何新的功能(至少在前端)。相反，我们存储文件的方式将会改变。我们将把它们持久化到 OCI 对象存储中，并使用 Rails 活动存储创建一个处理“附件”的全功能接口。

# 它不是什么

提醒一下大家，这不是为了“今天就去生产吧！”情况，而是展示如何在 Rails 应用程序中集成(使用)OCI 对象存储。这个特定的实现使用活动存储来处理附件的管理。安全性最佳实践并未得到强调，它更像是一个“展示和讲述”的例子。

# 入门指南

从我们在本系列的第一篇文章[中构建的 myfile 示例 Rails 应用程序的“干净”副本开始。您可以将 myfile 应用程序复制并粘贴到另一个目录(如 *myfile_active_storage* )或创建一个分支。无论哪种方式，我们将从我们离开的地方开始，所以拿起一杯茶，让我们开始吧！](/oracledevs/introducing-myfile-a-base-rails-app-on-oci-a91149812cbc)

# 创建 OCI 对象存储桶

首先，我们需要创建一个桶。我选择用 Terraform 来做这件事。下面是一个示例 Terraform 代码片段(提供者等。未示出-仅是与桶相关的几个相关摘录):

```
data "oci_objectstorage_namespace" "this" {
  compartment_id = var.tenancy_ocid
}resource "oci_objectstorage_bucket" "test_bucket" {
  compartment_id = var.compartment_ocid
  name = "myfile-development"
  namespace = data.oci_objectstorage_namespace.this.namespace
  access_type = "NoPublicAccess"
  storage_tier = "Standard"

  lifecycle {
    ignore_changes = [defined_tags["Oracle-Tags.CreatedBy"], defined_tags["Oracle-Tags.CreatedOn"]]
  }
}
```

上面的代码片段在一个特定的区间中创建了一个名为 *myfile-development* 的 bucket。该存储桶是私有的(公共访问是*而不是*授予的)，并且使用*标准*存储层。虽然其中一些可能是默认设置，但我喜欢明确地把它拼写出来，让人们痛苦地明白正在做什么。不熟悉 OCI Terraform 提供者和 oci_objectstorage_bucket 资源的人仍然可以从上面的内容中推断出一些基础知识。

如果您愿意，您可以使用 OCI 控制台来创建这个桶。如果您不熟悉如何操作，请参见 [OCI 文档](https://docs.oracle.com/en-us/iaas/Content/Object/Tasks/managingbuckets.htm#ariaid-title18)中的[说明](https://docs.oracle.com/en-us/iaas/Content/Object/Tasks/managingbuckets.htm#ariaid-title18)。

在我们完全完成之前，还有一个为 [OCI S3 兼容性 API](https://docs.oracle.com/en-us/iaas/Content/Object/Tasks/s3compatibleapi.htm) 设置凭证的问题。遵循在 [OCI S3 兼容性 API](https://docs.oracle.com/en-us/iaas/Content/Object/Tasks/s3compatibleapi.htm) 文档中标题为*设置对 Oracle 云基础设施的访问*一节中设置客户密钥的说明。请将这些凭证存放在安全的地方，因为您很快就会用到它们！

# 镜像数据

活动存储提供了将数据从一个位置镜像到另一个位置的能力。这可能如何使用？最简单的方法之一是从一个 OCI 地区复制到另一个地区。使用 [OCI 对象存储复制](https://docs.oracle.com/en-us/iaas/Content/Object/Tasks/usingreplication.htm)功能，该功能在 OCI 本地也可用。

哪个最好？这取决于你。主动存储方法的一个危险信号是它不是原子的。活动存储镜像服务文档非常清楚镜像不是一个“有保证的”动作。我倾向于使用 OCI 本地的功能，这将提供不依赖于我的应用程序代码的稳定性能。在我看来，这保证了更高层次的保证，它会被正确地完成。

不过，我们有些超前了——我们甚至还没有在项目中设置活动存储。就这么办吧！

# 添加活动存储

活动存储使用几个需要创建的表([活动存储文档](https://guides.rubyonrails.org/active_storage_overview.html)告诉你更多关于这些的信息)。这是通过以下命令完成的:

```
$ rails active_storage:install
```

这将创建负责创建三个表的迁移。使用以下内容运行迁移:

```
$ rake db:migrate
```

现在，我们已经有了在应用程序中使用活动存储的基本基础架构。我们需要告诉活动存储哪些存储位置是可用的:用类似下面的内容配置 *config/storage.yml* :

```
# config/storage.ymloci:
  service: S3
  access_key_id: <%= ENV['OCI_KEY'] %>
  secret_access_key: <%= ENV['OCI_SECRET'] %>
  bucket: myfile-<%= Rails.env %>
  region: <%= ENV['OCI_REGION'] %>
  endpoint: <OCI S3 Compatibility API URL >
  force_path_style: true
```

我将避免无休止地争论哪个更好(更安全):配置文件(当然是锁定的，不提交给回购)或环境变量。在这个例子中，我们将使用环境变量。不要担心这里使用的环境名称！你可以随意修改……你会知道怎么做的。

端点参数应该设置为正确的 URL。参考[文档](https://docs.oracle.com/en-us/iaas/api/#/en/s3objectstorage/20160918/)查看不同的 [OCI S3 兼容 API 端点](https://docs.oracle.com/en-us/iaas/api/#/en/s3objectstorage/20160918/)列表。您还需要获得您的 OCI 对象存储名称空间来完成 API。上面的 Terraform 获取名称空间，但也可以通过 OCI 控制台、OCI CLI 或其他方法获取。参见 [OCI 对象存储命名空间文档](https://docs.oracle.com/en-us/iaas/Content/Object/Tasks/understandingnamespaces.htm)了解更多关于如何使用 OCI 命名空间的信息。

这里有一个更好的端点解决方案(同样，这将放在 *config/storage.yml* )中:

```
# config/storage.ymoci:
  service: S3
  access_key_id: <%= ENV['OCI_KEY'] %>
  secret_access_key: <%= ENV['OCI_SECRET'] %>
  bucket: myfile-<%= Rails.env %>
  region: <%= ENV['OCI_REGION'] %>
  endpoint: https://<%= ENV['OCI_NAMESPACE'] %>.compat.objectstorage.<%= ENV['OCI_REGION'] %>.oraclecloud.com
  force_path_style: true
```

通过简单地提供两个以上的环境变量(或者这些可以是配置文件设置)，我们找到了一种方法，使我们可以轻松地在解决方案中使用不同的 OCI 地区，而不必更改端点 URL。

注意: *force_path_style* 参数必须设置为 true，否则会出现如下错误:

```
Aws::S3::Errors::MalformedXML (The XML you provided was not well-formed or did not validate against our published schema.):
```

编辑*config/environments/development . Rb*并确保出现如下所示的行:

```
# config/environments/development.rb# use OCI Object Storage for file storage
config.active_storage.service = :oci
```

请注意，config.active_storage.service 设置可能已经有一行了(我的设置为 use :local)。

在我们开始之前，请注意我们如何将 *S3* 服务用于 *oci* 存储服务？我们需要安装 *aws-sdk-s3* gem 来工作。现在让我们通过将以下内容添加到 *Gemfile* 中来实现这一点:

```
# Gemfilegem "aws-sdk-s3", require: false
```

(这是直接从[活动存储文档](https://guides.rubyonrails.org/active_storage_overview.html)中复制的)

让我们运行 Bundler 以确保所有的 gem 都准备好供我们使用:

```
$ bundle
```

有了基本的“基础设施”(表、存储位置定义等。)现在，我们已经准备好迁移我们的应用程序，以使用活动存储(而不是我们自己的自制解决方案)。

# 修订模型结构

如果您看一下 Active Storage 为我们创建的迁移(使用*rails Active _ Storage:install*命令)，您会注意到 *active_storage_blobs* 表中有几个字段与我们自己的 *things* 表中的字段重复: *filename* 和 *content_type* 。*active _ storage _ attachments*表具有 *blob* 列，它存储实际的二进制文件数据。原来我们不再需要在 *things* 表中为这些数据点拥有自己的列！让我们生成一个新的[迁移](https://guides.rubyonrails.org/active_record_migrations.html)来去掉几个不需要的列:

```
$ rails g migration RemoveThingsColumns
```

您将在 *db/migrate* 中找到该文件(它还会告诉您 *create* 行中的路径)。继续修改您的代码，如下所示:

```
class RemoveThingsColumns < ActiveRecord::Migration[7.0]
  def change
    remove_column :things, :file_name, :string
    remove_column :things, :content_type, :string
    remove_column :things, :file_data, :binary
    add_column :things, :document, :attachment
  end
end
```

我们将从*事物*表中删除三列(*文件名*、*内容类型*和*文件数据*)，并添加一个新列(*附件*)，这将是我们与活动存储品质的链接！这意味着我们将有一个*事物*记录，它可以通过活动存储(即*:附件*类型)附加(和管理)一个*文档*。多棒啊。！我喜欢这个。通过运行以下命令来应用迁移:

```
$ rake db:migrate
```

现在，我们准备在应用程序中更新模型/控制器。首先，更新模型以建立一个*事物*和一个单独的*文档*之间的关系。修改 *app/models/thing.rb* 如下所示:

```
# app/models/thing.rbclass Thing < ApplicationRecord
  has_one_attached :document
end
```

现在我们来编辑*app/controllers/things _ controller . Rb*。滚动到底部，在 private 下，从以下位置更新 *thing_params* 方法:

```
# app/controllers/things_controller.rb def thing_params
      ret = params.require(:thing).permit(:description)
      ret.merge( { file_data: params[:thing][:file_data].read, file_name: params[:thing][:file_data].original_filename, content_type: params[:thing][:file_data].content_type } )
    end
```

对此:

```
# app/controllers/things_controller.rb def thing_params
      params.require(:thing).permit(:description, :document)
    end
```

在同一个控制器中，更改以下内容:

```
# app/controllers/things_controller.rbclass ThingsController < ApplicationController
  before_action :set_thing, only: %i[ show edit update destroy download ]
```

对此:

```
# app/controllers/things_controller.rbclass ThingsController < ApplicationController
  before_action :set_thing, only: %i[ show edit update destroy ]
```

并完全删除下载方法(不再需要它，因为活动存储已经为我们解决了这个问题！):

```
# app/controllers/things_controller.rb def download
    # credit to [https://piotrmurach.com/articles/streaming-large-zip-files-in-rails/](https://piotrmurach.com/articles/streaming-large-zip-files-in-rails/) for the header to set
    response.set_header('Content-Disposition', "attachment; filename=\"#{[@thing](http://twitter.com/thing).file_name}\"")
    response.content_type = [@thing](http://twitter.com/thing).content_type
    response.write([@thing](http://twitter.com/thing).file_data)
  end
```

删除*下载*的路径，编辑 */config/routes.rb* ，修改如下:

```
# config/routes.rbRails.application.routes.draw do
  resources :things do
    get 'download', on: :member
  end
  # Define your application routes per the DSL in [https://guides.rubyonrails.org/routing.html](https://guides.rubyonrails.org/routing.html)# Defines the root path route ("/")
  root "things#index"
end
```

对此:

```
# config/routes.rbRails.application.routes.draw do
  resources :things
  # Define your application routes per the DSL in [https://guides.rubyonrails.org/routing.html](https://guides.rubyonrails.org/routing.html)# Defines the root path route ("/")
  root "things#index"
end
```

编辑(或创建，如果缺少的话)*config/initializers/active _ storage . Rb*，确保以下行存在:

```
# config/initializers/active_storage.rbRails.application.config.active_storage.resolve_model_to_route = :rails_storage_proxy
```

让我们继续更新不同的视图。修改*app/views/things/_ form . html . erb*，更改如下:

```
# app/views/things/_form.html.erb <div>
    <%= form.label :file_data, style: "display: block" %>
    <%= form.file_field :file_data %>
  </div>
```

对此:

```
# app/views/things/_form.html.erb <div>
    <%= form.label "Document", style: "display: block" %>
    <%= form.file_field :document %>
  </div>
```

继续并更改*app/views/things/_ thing . html . erb*如下:

```
# app/views/things/_thing.html.erb<div id="<%= dom_id thing %>">
  <p>
    <strong>File name:</strong>
    <%= link_to thing.file_name, download_thing_path(thing) %>
  </p><p>
    <strong>Content type:</strong>
    <%= thing.content_type %>
  </p><p>
    <strong>File data length (bytes):</strong>
    <%= thing.file_data.length %>
  </p><p>
    <strong>Description:</strong>
    <%= thing.description %>
  </p></div>
```

对此:

```
# app/views/things/_thing.html.erb<div id="<%= dom_id thing %>">
  <% if thing.document.attached? %>
  <p>
    <strong>File name:</strong>
    <%= link_to thing.document.filename, rails_storage_proxy_path(thing.document) %>
  </p><p>
    <strong>Content type:</strong>
    <%= thing.document.content_type %>
  </p><p>
    <strong>File data length (bytes):</strong>
    <%= thing.document.byte_size %>
  </p>
  <% else %>
  <p>No document is attached to this <i>thing</i>.</p>
  <% end %>

  <p>
    <strong>Description:</strong>
    <%= thing.description %>
  </p></div>
```

记住，这不是关于*好的* Ruby/Rails 代码，而是关于如何获得使用 OCI 对象存储的积极支持。为此，我走了一些捷径，最终在视图中嵌入了一些逻辑(糟糕，糟糕，糟糕)。由此产生的 ERB 模板相当混乱。我们真的应该使用类似于[小胡子](https://github.com/mustache/mustache)的东西将模板和逻辑分开。我明白了。你明白了。同样，我们让事情变得非常简单:我不想给画面添加这种额外的“混乱”,而是将注意力放在手头的任务上:让活动存储使用 OCI 对象存储。

我们现在已经准备好完成这件事了！用如下代码启动它(这适用于许多* nix shells):

```
$ export OCI_KEY=<YOUR OCI KEY HERE>
$ export OCI_SECRET=<YOUR OCI SECRET HERE>
$ export OCI_NAMESPACE=<YOUR OCI NAMESPACE HERE>
$ export OCI_REGION=<YOUR OCI REGION HERE>
```

OCI 地区需要成为它的地区标识符。查看 [OCI 文档](https://docs.oracle.com/en-us/iaas/Content/General/Concepts/regions.htm)中的地区和标识符列表，了解更多信息。然后你运行它:

```
$ rails s
```

出于某种原因，我在这一点上出现了一个错误。如果您遇到同样的问题“link_tree 参数必须是一个目录”，我进入 app/assets/config/manifest.js 并删除了它所抱怨的令人不快的行。在我的例子中，下面一行是问题所在(我已经删除了):

```
//= link_tree ../../../vendor/javascript .js
```

从那时起，事情进展顺利。

# 我们拥有的

活动存储为在我们的应用程序中处理文件提供了一个非常好的接口。在*文档*附件中有几种不同的方法。在轨道控制台中跟随自己(运行*轨道 c* ):

```
t = Thing.last
# this will tell us whether or not a file is attached
t.document.attached?
# here's how to get the file size
t.document.byte_size
# self-explanatory
t.document.filename.to_s
# again, no explanation needed here
t.document.content_type
```

这只是可用属性中的一小部分。查看此处的[和此处的](https://api.rubyonrails.org/classes/ActiveStorage.html)和[以了解有关活动存储提供的一些优秀方法和属性的更多信息。](https://www.rubydoc.info/gems/activestorage)

# 清理

这很有趣，对吧？！现在是时候收拾我们留下的烂摊子了。如果您试图删除*my file-development*bucket(或者您选择的任何名称)，您可能会遇到一个错误。这是因为要删除一个存储桶，它必须是空的。您可以遍历所有不同的对象并手动删除它们，但这真的很痛苦。在此过程中，我们不只是删除对象存储中的对象，而是继续保持本地数据库的整洁，完成后删除每件事的记录(和对象)。开始了…打开 rails 控制台( *rails c* )，然后发出下面的命令(不要忘记确保首先设置好您的环境变量！):

```
Thing.all.each do |t|
  t.document.purge
  t.destroy
end
```

有人可能认为简单地调用 *Thing.destroy_all* 就可以了，但我发现这有问题。上面几行彻底销毁了对象(一个接一个地“清除”)并销毁了本地 SQLite3 数据库中的记录(销毁了实际的“thing”资源)。

在这一点上，继续删除我们一直使用的 OCI 对象存储桶( *myfile-development* )是安全的，因为它现在应该是空的。

# 资源

这里有一些很棒的参考资料和资源，你可能会觉得有用(我找到了！):

*   [https://docs . Oracle . com/en-us/iaas/Content/Object/Tasks/S3 compatibleapi . htm](https://docs.oracle.com/en-us/iaas/Content/Object/Tasks/s3compatibleapi.htm)
*   [https://docs . Oracle . com/en-us/iaas/API/#/en/S3 objectstorage/2016 09 18/](https://docs.oracle.com/en-us/iaas/api/%23/en/s3objectstorage/20160918/)
*   [https://registry . terra form . io/providers/hashi corp/OCI/latest/docs/resources/object storage _ bucket](https://registry.terraform.io/providers/hashicorp/oci/latest/docs/resources/objectstorage_bucket)
*   [https://www.oracle.com/cloud/storage/object-storage/faq/](https://www.oracle.com/cloud/storage/object-storage/faq/)
*   [https://guides . ruby on rails . org/active _ record _ migrations . html](https://guides.rubyonrails.org/active_record_migrations.html)
*   [https://guides.rubyonrails.org/active_storage_overview.html](https://guides.rubyonrails.org/active_storage_overview.html)
*   [https://www . ruby guides . com/2019/01/ruby-environment-variables/](https://www.rubyguides.com/2019/01/ruby-environment-variables/)
*   [https://www.rubydoc.info/gems/activestorage](https://www.rubydoc.info/gems/activestorage)
*   [https://api.rubyonrails.org/classes/ActiveStorage.html](https://api.rubyonrails.org/classes/ActiveStorage.html)

# 包装它

这是一次有趣的小旅程。主动存储正在蓬勃发展。碰巧的是，它与 OCI 对象存储*配合得非常好*！并不是说我有任何疑问，但是做一个快速演示，向您展示这种集成是多么容易，这很棒。希望你已经发现这至少有点有趣，如果没有帮助，参考。这对我来说是一个很好的练习，允许我记录一些特定的集成点。

# 下一步是什么

如果您已经在使用特定的 gem(而不是活动存储)来处理文件附件，该怎么办？看看 myfile 如何与其他一些文件处理工具(而不是活动存储)一起工作不是很好吗？并不是说活动存储有什么问题…我认为它实际上真的很酷。但是，如果您已经有了一个最喜欢的处理附件的工具，并且希望继续使用(和 OCI 对象存储一起)，该怎么办呢？这是一个有趣的想法…请继续关注一些即将到来的迭代。直到下一次，保持位流。