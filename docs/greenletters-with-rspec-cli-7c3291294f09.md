# 使用 Rspec CLI 的 GreenLetters

> 原文：<https://medium.com/globant/greenletters-with-rspec-cli-7c3291294f09?source=collection_archive---------0----------------------->

## 拯救者绿信

## CLI RSpec 变得简单

你曾经尝试过为 CLI 应用程序编写 ruby 规范吗？

如果是的话，你可能已经熟悉了`**Expect**` gem，这是编写 CLI 规范时最常用的。在使用 Expect gem 的过程中发现了不一致的结果，比如:
1。不一致的执行结果和覆盖率，也就是说，在连续的构建之后，结果是变化的。

为了克服这些矛盾，我使用了 Greenletters Gem。

## 什么是绿信宝石？

`**Greenletters**`是一个控制台交互自动化库，主要用于编写小黄瓜自动化步骤的规范。我用它来编写 CLI 规范。我们将了解如何利用这个 gem 来编写 CLI RSpec。

# 建立创业板

让我们首先为新的 gem 创建文件结构。创建一个新目录。我们将创造`helloworld`宝石。
要设置 CLI，我们需要使用以下命令创建一个 gem。

```
mkdir helloworld
cd helloworld
```

我们的 gem 将包含一个`.gemspec`来指定 gem 的所有配置，`lib`文件夹将是源文件。：

```
touch helloworld.gemspec
mkdir lib
touch lib/helloworld.rb
mkdir bin
```

文件结构将如下所示:

```
helloworld
|-- helloworld.gemspec
|-- bin
    |-- helloworld
|-- lib
    |-- helloworld.rb
|-- spec
    |-- spec_helper.rb
    |-- helloworld_spec.rb
```

让我们编辑`helloworld.gemspec`并添加关于我们的宝石的信息。用你自己的宝石特有的信息更新这个。

```
Gem**::**Specification.**new** **do** **|**s**|**
  s.**name**      **=** 'helloworld'
  s.**version**   **=** '0.0.0'
  s.**platform**  **=** Gem**::**Platform**::**RUBY
  s.**summary**   **=** 'HelloWorld gem'
  s.**description** **=** "Helloworld in showing Ruby CLI Spec!"
  s.**authors**   **=** ['Kajal Mohite']
  s.**email**     **=** ['test@gmail.com']
  s.**homepage**  **=** 'http://rubygems.org/gems/helloworld'
  s.**license**   **=** 'MIT'
  s.**files**     **=** Dir.**glob**("{lib,bin}/**/*") *# This includes all files under the lib directory recursively, so we don't have to add each one individually.*
  s.**require_path** **=** 'lib'
  s.**executables** = ['helloworld']**end**
```

Gem 配置已经完成，现在让我们添加基本的 ruby 代码并进行测试。为了实现这一点，让我们编辑`lib/helloworld.rb`:

```
**module** HelloWorld
  **def** **self.hello_world**
    "Good morning world!"
  **end**
**end**
```

Bin 文件夹`**bin/helloworld**`:

```
#!/usr/bin/env rubyrequire 'helloworld'puts HelloWorld::hello_world
```

太好了。我们已经准备好了。让我们现在构建和测试。

# 构建和测试

要打包所有内容，请传递`.gemspec`文件作为输入:

```
gem build helloworld.gemspec
```

成功执行后，您将看到以下内容:

```
gem build helloworld.gemspec
Successfully built RubyGem
Name: helloworld
Version: 0.0.0
File: helloworld-0.0.0.gemVersion: 0.0.0File: helloworld-0.0.0.gem
```

让我们安装它，并从我们的代码中访问它。

```
gem install helloworld-0.0.0.gem
```

# Spec_helper.rb

现在在 spec_helper.rb 中配置 RSpec:
在 spec_helper.rb 中设置 RSpec 配置

```
require 'helloworld'RSpec.configure do |config| config.disable_monkey_patching! config.expect_with :rspec do |c| c.syntax = :expect end config.mock_with :rspec do |c| c.syntax = %i[expect should] endend
```

让我们在 **helloworld_spec.rb** 中编写应用相关的 spec

```
require 'greenletters'require 'spec_helper'RSpec.describe HelloWorld do describe '#start: with default config Y' do adv = Greenletters::Process.new('helloworld', timeout: 20, transcript: $stdout) adv.start! it 'should display loading statement' do adv.wait_for(:output, /Good morning world!/) end endend
```

# 规格执行

执行规范的命令是`rspec spec-file-path`它将执行文件中写入的所有测试用例并显示结果。

```
**$** rspec spec/helloworld_spec.rbGood morning world!.Finished in 0.68765 seconds (files took 0.3343 seconds to load)1 example, 0 failures
```

完美！

这样，我们可以创建任何 CLI 应用程序，并使用 GreenLetters gem 的 ease 语法对其进行测试。

# 参考

[https://stack overflow . com/questions/7142978/is-there-a-a-expect-equivalent-gem-for-ruby](https://stackoverflow.com/questions/7142978/is-there-an-expect-equivalent-gem-for-ruby)

[https://avdi . codes/green letters-无痛自动化和测试命令行应用程序/](https://avdi.codes/greenletters-painless-automation-and-testing-for-command-line-applications/)

[https://github.com/avdi/greenletters](https://github.com/avdi/greenletters)