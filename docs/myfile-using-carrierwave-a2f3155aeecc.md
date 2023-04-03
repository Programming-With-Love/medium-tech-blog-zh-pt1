# 我的文件—使用载波

> 原文：<https://medium.com/oracledevs/myfile-using-carrierwave-a2f3155aeecc?source=collection_archive---------2----------------------->

![](img/7291db5ead7ac882efa84aa0e6501589.png)

Photo by [Lucas Meneses](https://www.pexels.com/@lucasmenesesphoto?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels) from [Pexels](https://www.pexels.com/photo/blue-ocean-waves-7795312/?utm_content=attributionCopyText&utm_medium=referral&utm_source=pexels)

# 先决阅读

本文是一个系列的一部分，介绍处理文件存储的各种流行的 Ruby gems，以及它们如何使用 OCI 对象存储作为存储位置。如果这是您第一次看这篇文章，请看一下第一篇文章，它给出了一个非常简单的应用程序示例(名为 *myfile* ),它是在后续文章的基础上构建的，每篇文章关注不同的宝石。

到目前为止，我们已经有文章介绍了使用 Rails 活动存储的示例[实现](/oracledevs/myfile-using-active-storage-23b37f3f8085)和使用蜻蜓的另一个实现[。也许值得看一看这些文章，了解一些背景知识。](/@timclegg/myfile-file-attachment-implementation-using-dragonfly-305641e0f163)

# 这是什么

这只是一个如何将 [CarrierWave](https://github.com/carrierwaveuploader/carrierwave) Ruby gem 用于 OCI 对象存储的示例实现。

# 它不是什么

这并不意味着按原样部署到生产中。它没有经过强化、测试，也不足以满足任何生产级使用案例的需求。

# 入门指南

你需要一个 OCI 账户(注册一个 OCI 账户[这里](https://www.oracle.com/cloud/free/#always-free?source=:ex:tb:::::WWMK220126P00006&SC=:ex:tb:::::WWMK220126P00006&pcode=WWMK220126P00006))。你想合作吗？在下面评论和/或加入 [Slack](https://join.slack.com/t/oracledevrel/shared_invite/zt-uffjmwh3-ksmv2ii9YxSkc6IpbokL1g) 的乐趣！

您将需要一个在 OCI 租赁中创建的 OCI 对象存储桶。查看[第一篇文章](/oracledevs/myfile-using-active-storage-23b37f3f8085)(参见标题为*创建 OCI 对象存储桶*的部分)以获得如何做的指导。

# 迁移到载波波

[CarrierWave](https://github.com/carrierwaveuploader/carrierwave) 使用 [Fog::Storage](https://fog.io/storage/) 时使用 S3 兼容(以及许多其他云)存储解决方案。这不是我们第一次使用 Fog::Storage，蜻蜓也用过(见[文章](/@timclegg/myfile-file-attachment-implementation-using-dragonfly-305641e0f163)关于[使用蜻蜓和我的文件](/@timclegg/myfile-file-attachment-implementation-using-dragonfly-305641e0f163))。

首先将我们需要的两种宝石添加到宝石文件中:

```
# Gemfilegem 'carrierwave', '~> 2.0'
gem 'fog-aws'
```

然后让 Bundler 安装它们:

```
$ bundle
```

使用一个新的生成器(名为*上传器*)生成一个名为*文档*的新上传器:

```
$ rails g uploader Document
```

编辑上传程序，使其包含以下内容:

```
# app/uploaders/document_uploader.rbclass DocumentUploader < CarrierWave::Uploader::Base
  storage :fog
end
```

这告诉 CarrierWave 使用 Fog 来存储文件。

与其他实现类似，我们需要为这个特定的实现删除几列(并添加另一列):

```
$ rails g migration RemoveThingsColumns
```

继续，让它的内容看起来像:

```
# db/migrate/<numbers>_remove_things_columns.rbclass RemoveThingsColumns < ActiveRecord::Migration[7.0]
  def change
    remove_column :things, :file_name, :string
    remove_column :things, :content_type, :string
    remove_column :things, :file_data, :binary

    add_column :things, :document, :string
  end
end
```

然后应用它:

```
$ rake db:migrate
```

创建一个新文件，并用以下内容填充它:

```
# config/initializers/carrierwave.rbCarrierWave.configure do |config|
  config.fog_credentials = {
    provider: 'AWS',
    aws_access_key_id: ENV['OCI_KEY'],
    aws_secret_access_key: ENV['OCI_SECRET'],
    region: ENV['OCI_REGION'],
    endpoint: "[https://#{](/oracledevs/myfile-using-carrierwave-a2f3155aeecc#%7B) ENV["OCI_NAMESPACE"] }.compat.objectstorage.#{ ENV["OCI_REGION"] }.oraclecloud.com",

    connection_options: {
      ssl_verify_peer: false
    },
    path_style: true,
    enable_signature_v4_streaming: false
  }
  config.fog_directory = "myfile-#{ Rails.env }"
  config.fog_public = false
  config.fog_attributes = {}
  config.cache_storage = :file
end
```

如果您已经阅读了其他文章，这应该看起来很熟悉。由于我们使用的是 Fog::Storage，这与我们在蜻蜓使用的非常相似(两者都使用 Fog)。

# 如果不使用:file for cache_storage

上面配置中的最后一行是特定于 CarrierWave 的。*config . cache _ storage =:file*行。这告诉 CarrierWave 在本地存储临时文件(而不是在 OCI 对象存储中)。

当我让 CarrierWave 使用 OCI 对象存储作为临时文件存储时，它并没有很好地完成清理工作。更正:它没有在 OCI 对象存储中进行任何清理。我尝试使用(在 Rails 控制台中)清理临时文件:

```
CarrierWave.clean_cached_files!
```

但是我没有看到这适用于 OCI 对象存储中的文件。最后，我使用下面的代码(也在 Rails 控制台中运行)来删除临时文件(使用 Fog::Storage 来删除存储桶中剩余的任何内容，我在清理完所有的 *Thing* objects 之后运行了该代码)。

```
conn = Fog::Storage.new({
  provider: 'AWS',
  aws_access_key_id: ENV['OCI_KEY'],
  aws_secret_access_key: ENV['OCI_SECRET'],
  region: ENV['OCI_REGION'],
  endpoint: "[https://#{](/oracledevs/myfile-using-carrierwave-a2f3155aeecc#%7B) ENV["OCI_NAMESPACE"] }.compat.objectstorage.#{ ENV["OCI_REGION"] }.oraclecloud.com",
  host: "#{ ENV["OCI_NAMESPACE"] }.compat.objectstorage.#{ ENV["OCI_REGION"] }.oraclecloud.com",    
  connection_options: {
    ssl_verify_peer: false
  },
  path_style: true,
  enable_signature_v4_streaming: false
})d = conn.directories.get("myfile-#{Rails.env}")
d.files.each {|f| f.destroy }
```

注意:删除东西的时候一定要小心！以上只是一个示例——在运行它之前，确保它适合您的环境。

如果你使用行*config . cache _ storage =:file*你应该不需要上面的信息。我把它放在这里是出于好奇，或者如果有人遇到了同样的副作用。

# MVC 迁移

让我们继续移植应用程序的其余部分，使模型看起来像这样:

```
# app/models/thing.rbclass Thing < ApplicationRecord
  mount_uploader :document, DocumentUploader
end
```

并将*#下载*、*#创建*和 *#thing_params* 控制器方法更新为:

```
# app/controllers/things_controller.rbclass ThingsController < ApplicationController
  before_action :set_thing, only: %i[ show edit update destroy download ]

  def download
    response.set_header('Content-Disposition', "attachment; filename=\"#{ [@thing](http://twitter.com/thing).document.identifier}\"")
    response.content_type = [@thing](http://twitter.com/thing).document.content_type
    response.write([@thing](http://twitter.com/thing).document.read)
  end

  ...# POST /things or /things.json
  def create
    [@thing](http://twitter.com/thing) = Thing.new(thing_params)
    [@thing](http://twitter.com/thing).document = thing_params[:document]

    respond_to do |format|
      if [@thing](http://twitter.com/thing).save
        format.html { redirect_to thing_url([@thing](http://twitter.com/thing)), notice: "Thing was successfully created." }
        format.json { render :show, status: :created, location: [@thing](http://twitter.com/thing) }
      else
        format.html { render :new, status: :unprocessable_entity }
        format.json { render json: [@thing](http://twitter.com/thing).errors, status: :unprocessable_entity }
      end
    end
  end...private
    # Use callbacks to share common setup or constraints between actions.
    def set_thing
      [@thing](http://twitter.com/thing) = Thing.find(params[:id])
    end# Only allow a list of trusted parameters through.
    def thing_params
      ret = params.require(:thing).permit(:description, :document)
    end
end
```

最后，我们要做一些视图更改，首先是查看一个*东西*(记住这是一段可怕的代码，但是展示了它可能是如何工作的):

```
# app/views/things/_thing.html.erb<div id="<%= dom_id thing %>">
  <% if thing.document.nil? == false && !thing.document.identifier.nil? %>
  <p>
    <strong>File name:</strong>
    <%= link_to thing.document.identifier, download_thing_path(thing) %>
  </p><p>
    <strong>Content type:</strong>
    <%= thing.document.content_type %>
  </p><p>
    <strong>File data length (bytes):</strong>
    <%= thing.document.length %>
  </p>
  <% end %><p>
    <strong>Description:</strong>
    <%= thing.description %>
  </p></div>
```

更新用于一件*事情*的表格:

```
# app/views/things/_form.html.erb<%= form_with(model: thing) do |form| %>
  <% if thing.errors.any? %>
    <div style="color: red">
      <h2><%= pluralize(thing.errors.count, "error") %> prohibited this thing from being saved:</h2><ul>
        <% thing.errors.each do |error| %>
          <li><%= error.full_message %></li>
        <% end %>
      </ul>
    </div>
  <% end %><div>
    <%= form.label 'File', style: "display: block" %>
    <%= form.file_field :document %>
  </div><div>
    <%= form.label :description, style: "display: block" %>
    <%= form.text_field :description %>
  </div><div>
    <%= form.submit %>
  </div>
<% end %>
```

继续运行它，看看最终结果:

```
$ export OCI_KEY=<YOUR OCI KEY HERE>
$ export OCI_SECRET=<YOUR OCI SECRET HERE>
$ export OCI_NAMESPACE=<YOUR OCI NAMESPACE HERE>
$ export OCI_REGION=<YOUR OCI REGION HERE>
$ rails s
```

耶！事情如我们所期望的那样进行着。与其他解决方案相同(差)*我的文件*外观/感觉，但这是在后台使用 CarrierWave(以及 OCI 对象存储)！

# 清除

最后，为了进行清理，打开一个 Rails 控制台(确保您导出了与上面相同的环境变量):

```
$ rails c
```

然后运行以下代码:

```
Thing.all.each {|t| t.destroy }
```

就这么简单！

# 资源

*   【https://github.com/carrierwaveuploader/carrierwave 
*   [https://rubygems.org/gems/carrierwave/](https://rubygems.org/gems/carrierwave/)
*   [https://fog.io/storage/](https://fog.io/storage/)

# 摘要

这真是一次有趣的旅程。我们已经看到实现了几个流行的文件上传/下载处理 gem，它们都使用 OCI 对象存储进行文件存储。如果你遇到需要使用这些宝石之一的情况，希望你会发现这有所帮助。直到下一次，保持位流！