# 将 ruby-protobuf 反序列化优化 50%

> 原文：<https://medium.com/square-corner-blog/optimizing-ruby-protobuf-deserialization-by-50-8aa14ca330d9?source=collection_archive---------1----------------------->

## 如何使用 ruby-prof 查找代码中的热点

*由* [撰写*扎克瑞*](https://medium.com/u/a29cf9bf448c?source=post_page-----8aa14ca330d9--------------------------------) *。*

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

我在 Square 的一个俗称“WebScale”的团队工作，该团队负责从我们的 monorail 中提取遗留支付数据。我们最近的任务之一是推出新的内部服务，在 Square 上搜索支付。我们主要使用 protobuf 作为数据传输，一个单独的支付协议在序列化时大约为 1，500-2，000 字节，并且每个请求返回多达 500 条记录。

新服务推出后，来自 Ruby MRI 2.2.3 客户端的一些请求在几个端点上从 250 毫秒变成了 1600 毫秒。我们已经有了关于花费在 RPC 请求上的时间的度量，所以我们猜测 protobuf 反序列化是罪魁祸首。

为了验证我们的猜测，我们使用了 [ruby-prof](https://github.com/ruby-prof/ruby-prof) 来分析 ruby 中的代码(在本例中，是 [protobuf](https://github.com/ruby-protobuf/protobuf) gem)。RubyProf 是一个分析 MRI 代码的有用工具，下面是我们如何使用它来查找热点。

我们的测试案例:

```
require 'protobuf'
require 'ruby-prof'*# Just so we don't get spammed when testing*
Protobuf**.**print_deprecation_warnings **=** **false***# Construct a proto that mimics the size of our production responses*
encoded **=** RecordResponse**.**new(
  payment: 500**.**times**.**map **do** **|**i**|**
    Record**.**new( **.**.**.**test data**.**.**.** )
  **end**
)**.**encoderesult **=** RubyProf**.**profile **do**
  RecordResponse**.**decode(encoded)
**end**printer **=** RubyProf**::**FlatPrinter**.**new(result)
printer**.**print(
  STDOUT,
  *# Only want to focus on hotspots right now*
  min_percent: 1
)
```

RubyProf 支持多种打印报告的方式。我是用它出现在“名单第一”的*科学方法*来挑选平板打印机的。为了简洁起见，我只展示了花费超过 1%时间的代码。

```
Measure Mode: wall_time
Thread ID: 70093065552380
Fiber ID: 70093084071700
Total: 4.599523
Sort by: self_time %self      total      self      wait     child     calls  name
 16.25      0.748     0.748     0.000     0.000  2031000   Protobuf::Enum#to_i
 15.35      1.454     0.706     0.000     0.748    30000   Array#select
  7.30      0.745     0.336     0.000     0.410   140000   <Class::Protobuf::Decoder>#read_varint
  3.44      0.176     0.158     0.000     0.018    94000   Kernel#respond_to?
  3.32      1.156     0.152     0.000     1.003    72000   <Class::Protobuf::Decoder>#read_field
  2.44      0.508     0.112     0.000     0.396    72000   <Class::Protobuf::Decoder>#read_key
  2.26      0.298     0.104     0.000     0.194    72000   Protobuf::Message::Fields::ClassMethods#get_field
  2.00      0.159     0.092     0.000     0.067   166500   IO::generic_readable#readbyte
  1.76      0.124     0.081     0.000     0.043   171500   Numeric#nonzero?
  1.66      0.076     0.076     0.000     0.000   410500   Fixnum#&
  1.58      0.073     0.073     0.000     0.000   144000   Protobuf::Field::BaseField#repeated?
  1.48      0.068     0.068     0.000     0.000    69000   Protobuf::Field::BaseField#setter
  1.46      0.067     0.067     0.000     0.000   166500   StringIO#getbyte
  1.19      0.055     0.055     0.000     0.000     1500   Kernel#caller
  1.17      0.054     0.054     0.000     0.000    72000   Protobuf::Message::Fields::ClassMethods#field_store
  1.05      0.087     0.048     0.000     0.039    72000   Protobuf::Field::BaseField#packed?
  1.00      0.137     0.046     0.000     0.091    36003   Class#new
```

继续我们“列表第一”的科学方法，我们将深入研究 [Protobuf::Enum](https://raw.githubusercontent.com/ruby-protobuf/protobuf/4cbb1f7cb98bb5a63875f4fc0cdf0c8109feffa7/lib/protobuf/enum.rb) 。在那个类中有一些 to_i 的用法，但是最突出的一个是:

```
*# Public: Get an array of Enum objects with the given tag.*
*#*
*# tag - An object that responds to to_i.*
*#*
*# Examples*
*#*
*#   class StateMachine < ::Protobuf::Enum*
*#     set_option :allow_alias*
*#     define :ON, 1*
*#     define :STARTED, 1*
*#     define :OFF, 2*
*#   end*
*#*
*#   StateMachine.enums_for_tag(1) #=> [ #<StateMachine::ON=1>, #<StateMachine::STARTED=1> ]*
*#   StateMachine.enums_for_tag(2) #=> [ #<StateMachine::OFF=2> ]*
*#*
*# Returns an array with zero or more Enum objects or nil.*
*#*
**def** **self.enums_for_tag**(tag)
  enums**.**select **do** **|**enum**|**
    enum**.**to_i **==** tag**.**to_i
  **end**
**end**
```

代码中有关于该方法如何工作的很好的文档，这对我们来说很容易。我们在 protos 中使用了很多 Enum，环顾一下 [Protobuf::Enum](https://raw.githubusercontent.com/ruby-protobuf/protobuf/4cbb1f7cb98bb5a63875f4fc0cdf0c8109feffa7/lib/protobuf/enum.rb) 类，很明显 enum_for_tag 被频繁调用(在 Enum _ for _ tag 和 fetch 中)。看看 all_tags 和 values 方法，我们知道缓存枚举数据是安全的。在这种情况下，它是 O(n) - > O(1)的一个简单优化。我们只能映射 int - >枚举，因为你可以[别名枚举](https://developers.google.com/protocol-buffers/docs/proto?hl=en#enum)。不过，这种情况下没问题。

完整的改动在最后，但要点是:

```
**def** **self.mapped_enums**
  @mapped_enums **||=** enums**.**each_with_object({}) **do** **|**enum, hash**|**
    list **=** hash**[**enum**.**to_i**]** **||=** **[]**
    list **<<** enum
  **end**
**end****def** **self.enums_for_tag**(tag)
  mapped_enums**[**tag**.**to_i**]** **||** **[]**
**end**
```

然后在 RubyProf 中重新运行它

```
Measure Mode: wall_time
Thread ID: 70271059223040
Fiber ID: 70271070182440
Total: 3.308983
Sort by: self_time %self      total      self      wait     child     calls  name
 10.16      0.736     0.336     0.000     0.400   140000   <Class::Protobuf::Decoder>#read_varint
  4.92      0.182     0.163     0.000     0.019    94000   Kernel#respond_to?
  4.62      1.148     0.153     0.000     0.995    72000   <Class::Protobuf::Decoder>#read_field
  3.33      0.501     0.110     0.000     0.391    72000   <Class::Protobuf::Decoder>#read_key
  3.25      0.308     0.108     0.000     0.201    72000   Protobuf::Message::Fields::ClassMethods#get_field
  2.70      0.156     0.089     0.000     0.066   166500   IO::generic_readable#readbyte
  2.37      0.078     0.078     0.000     0.000   410500   Fixnum#&
  2.34      0.119     0.077     0.000     0.042   171500   Numeric#nonzero?
```

好多了！RubyProf 中列出的总时间是精确的节省百分比(~25%)，但是总时间更高，因为 RubyProf 挂钩到 Ruby 来分析所有的东西。没有 RubyProf，我们看到来自 Ruby MRI 2.2.3 客户端的请求从 1，000 毫秒下降到大约 750 毫秒。

由于 750 毫秒仍然太长，我们将看看下一个最慢的调用点 [Protobuf::Decoder](https://github.com/ruby-protobuf/protobuf/blob/58ca57e2aac6d5f9860e64534d5ef2d23c934ed2/lib/protobuf/decoder.rb) 和 read_varint 方法。

```
*# Read varint integer value from +stream+.*
**def** **self.read_varint**(stream)
  value **=** index **=** 0
  **begin**
    byte **=** stream**.**readbyte
    value **|=** (byte **&** 0x7f) **<<** (7 ***** index)
    index **+=** 1
  **end** **while** (byte **&** 0x80)**.**nonzero?
  value
**end**
```

代码足够简单，任何优化都是微不足道的。Ruby 对于我们正在进行的调用量来说太慢了，这使得它成为用 c 重写该方法的候选对象。事实证明，ruby-protocol-buffers gem(与我们使用的 ruby protobuf gem 不同)的作者发布了一个名为 [varint](https://github.com/liquidm/varint/blob/master/ext/varint/varint.c) 的 gem，它正是我们所需要的。

通常，我不喜欢用 C 语言替换 Ruby，尤其是从库中。不能保证 C 和 Ruby 代码会以同样的方式运行，调试和维护都更加困难。因为它已经被另一个 gem 使用，而且代码是独立的，所以我试了一下。

我们在测试用例中添加了一个简单的 monkeypatch:

```
**module** Protobuf
  **class** **FastVarint**
    **extend** **::**Varint
  **end** **class** **Decoder**
    **def** **self.read_varint**(stream)
      FastVarint**.**decode(stream)
    **end**
  **end**
**end**
```

重新运行 RubyProf

```
Measure Mode: wall_time
Thread ID: 70126242488840
Fiber ID: 70126246561280
Total: 2.722766
Sort by: self_time %self      total      self      wait     child     calls  name
  5.65      0.676     0.154     0.000     0.522    72000   <Class::Protobuf::Decoder>#read_field
  5.64      0.172     0.154     0.000     0.018    94000   Kernel#respond_to?
  4.55      0.283     0.124     0.000     0.159    72000   <Class::Protobuf::Decoder>#read_key
  4.04      0.302     0.110     0.000     0.192    72000   Protobuf::Message::Fields::ClassMethods#get_field
  3.48      0.250     0.095     0.000     0.156   140000   <Class::Protobuf::Decoder>#read_varint
  3.13      0.156     0.085     0.000     0.070   140000   Varint#decode
  2.86      0.078     0.078     0.000     0.000   144000   Protobuf::Field::BaseField#repeated?
  2.58      0.070     0.070     0.000     0.000   166500   StringIO#getbyte
  2.49      0.068     0.068     0.000     0.000    69000   Protobuf::Field::BaseField#setter
  2.10      0.058     0.057     0.000     0.000     1500   Kernel#caller
```

通过公然作弊，让 C 来做，我们得到了 18%的优化。即使有了这些改进，我们的解码时间仍然在 600 毫秒左右。

继续，我们再来看看 [Protobuf::Decoder](https://github.com/ruby-protobuf/protobuf/blob/58ca57e2aac6d5f9860e64534d5ef2d23c934ed2/lib/protobuf/decoder.rb) 。不幸的是，read_field 和 read_key 方法对我们来说太简单了，无法实现真正的优化。内核# respond _ to？为了安全起见，在太多的地方使用了，比如如果 to_i 不存在就返回 nil，而不是 error。

这实际上只给我们留下了[proto buf::Message::Fields::class methods](https://github.com/ruby-protobuf/protobuf/blob/71fd6ae9185c53764dd665da7e45e8fbb73853da/lib/protobuf/message/fields.rb)，除非我们想优化 50 个调用点，每个优化几毫秒。查看 get_field，我们看到:

```
**def** **get_field**(name_or_tag, allow_extension **=** **false**)
  name_or_tag **=** name_or_tag**.**to_sym **if** name_or_tag**.**respond_to?(:to_sym)
  field **=** field_store**[**name_or_tag**]** **if** field **&&** (allow_extension **||** **!**field**.**extension?)
    field
  **else**
    **nil**
  **end**
**end**
```

看上面的 RubyProf 跟踪，我们已经知道 respond_to？总共被调用了 94000 次。在添加了一些调试来统计调用数之后，结果是 get_field 占了 72，000 (75%)个要响应的调用？，而且它只调用 _ sym 2,000 次。基本上，只有 2.7%的人回复了？调用会导致。

而不是调用 respond_to？而 to_sym，我们可以缓存一个 string field -> field 的映射，使之成为两个 O(1)查找。get_field 方法如下所示:

```
**def** **get_field**(name_or_tag, allow_extension **=** **false**)
  field **=** field_store**[**name_or_tag**]** **||** str_field_store**[**name_or_tag**]** **if** field **&&** (allow_extension **||** **!**field**.**extension?)
    field
  **else**
    **nil**
  **end**
**end**
```

RubyProf 的最后一次重播

```
Measure Mode: wall_time
Thread ID: 70107917574660
Fiber ID: 70107924939720
Total: 2.428846
Sort by: self_time %self      total      self      wait     child     calls  name
  6.12      0.666     0.155     0.000     0.511    72000   <Class::Protobuf::Decoder>#read_field
  4.70      0.277     0.119     0.000     0.158    72000   <Class::Protobuf::Decoder>#read_key
  3.70      0.248     0.094     0.000     0.154   140000   <Class::Protobuf::Decoder>#read_varint
  3.39      0.132     0.086     0.000     0.046    72000   Protobuf::Message::Fields::ClassMethods#get_field
  3.39      0.154     0.086     0.000     0.068   140000   Varint#decode
  2.96      0.075     0.075     0.000     0.000   144000   Protobuf::Field::BaseField#repeated?
  2.70      0.068     0.068     0.000     0.000   166500   StringIO#getbyte
  2.60      0.066     0.066     0.000     0.000    69000   Protobuf::Field::BaseField#setter
```

这又给了我们 12%的提升，内核# respond _ to？被降到了 2.2 万次(1.29%的自己%)。总的来说，我们优化了 50%的反序列化，而且只花了 RubyProf 几个小时的调查。

RubyProf 是一个很棒的工具，你可以使用 GraphPrinter 或 CallTreePrinter 获得更多细节，它们可以与 [KCachegrind](http://kcachegrind.sourceforge.net/) 一起使用。对于我们的例子来说，FlatPrinter 很简单，代码很容易跟踪，可以缩小优化的范围。RubyProf 中各种打印机的例子可以在 repo 上的[中找到。](https://github.com/ruby-prof/ruby-prof/tree/master/examples)

我们将所有的变更上传到了 proto buf gem 中。您可以看到在[变量](https://github.com/ruby-protobuf/protobuf/pull/269)、[获取 _ 字段/获取 _ 扩展 _ 字段](https://github.com/ruby-protobuf/protobuf/pull/267)和[枚举](https://github.com/ruby-protobuf/protobuf/pull/264)优化 PRs 中所做的确切更改。如果您使用 protobuf gem，安装了 [varint](https://rubygems.org/gems/varint) gem(仅 MRI)的 v3.5.5 和更高版本将为您提供全套优化。

将来，我们会考虑迁移到 [protobuf3](https://github.com/google/protobuf/tree/master/ruby) 。Ruby gem 在 alpha 中，但是它是基于 Google 的第一方 [C++](https://github.com/google/protobuf) 库构建的。在上述优化的基准测试中，谷歌的实现比 ruby-protobuf 快了 7 倍。

[](https://twitter.com/zachanker) [## 扎卡里·安克尔(@zachanker) |推特

### Zachary Anker 的最新推文(@zachanker)。广场的工程师。旧金山

twitter.com](https://twitter.com/zachanker)