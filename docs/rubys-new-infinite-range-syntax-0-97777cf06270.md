# Ruby 新的无限范围语法:(0..)

> 原文：<https://medium.com/square-corner-blog/rubys-new-infinite-range-syntax-0-97777cf06270?source=collection_archive---------1----------------------->

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

# 介绍 Ruby 2.6 的无限范围

2018 年圣诞节，Ruby 2.6 将发布，支持表示无限范围的新语法:

```
42..
#=> 42..nil # yes, this is infinite!
```

那么我们为什么需要这个新语法呢？到目前为止，在 Ruby 中创建无限范围有点笨拙:

```
42..Float::INFINITY
#=> 42..Infinity
```

没有人喜欢一遍又一遍地输入`Float::INFINITY`或`1.fdiv(0)`。作为 Ruby 的惯用方式，这似乎不是一个好的解决方案。然而，一些 ruby 专家认为我们不需要无限的范围，因为`Integer#step`已经允许你从`n`迭代到无穷大:

```
42.step
#<Enumerator: ...>42.step.size
#=> Infinity
```

最终，Matz(Ruby 的创造者)决定接受无限范围语法的提议，他说，“我真的觉得应该有一种流畅的方式来做`0..Float::INFINITY`”

# 用法示例

我们能用这个新语法做什么呢？我们可以做的一件有用的事情是访问元素，直到序列中的最后一个元素:

```
[:a, :b, :c, :d, :e][2..]
#=> [:c, :d, :e]'012345'[2..]
#=> "2345"
```

在以前版本的 Ruby 中，我们会使用`-1`来表示我们希望所有的元素直到最后一个元素:

```
[:a, :b, :c, :d, :e][2..-1]
#=> [:c, :d, :e]'012345'[2..-1]
#=> "2345"
```

范围也响应三个等式，所以我们现在可以用 case 语句做一些简单的事情:

```
case 2022
when(2030..)
  :mysterious_future
when(2020..)
  :twenties
when(2010..)
  :nowish
else
  :ancient_past
end
#=> :twenties
```

新范围的另一个用途是将它们与`#zip`一起使用，以逼近`#each`、`#with_index`和`#to_a`的组合:

```
[:a, :b, :c].zip(42..)
#=> [[:a, 42], [:b, 43], [:c, 44]# instead of:[:a, :b, :c].each.with_index(42).to_a
#=> [[:a, 42], [:b, 43], [:c, 44]]
```

或者，两个无限的范围甚至可以懒洋洋地压缩在一起:

```
(:a..).lazy.zip(42..).first(3)
#=> [[:a, 42], [:b, 43], [:c, 44]]
```

最后，对于无限范围，最有用的事情之一就是无限迭代:

```
(42..).each { |n| # forever! }
```

# 结论

新的无限范围语法将于 2018 年 12 月 25 日与 Ruby 2.6 一起发布。如果你想在那之前玩它，试试[夜间 Ruby 快照](https://cache.ruby-lang.org/pub/ruby/snapshot.tar.gz)。或者会包含在即将发布的 ruby-2.6.0-preview2 中。我们要感谢 Yusuke Endoh 的新语法，因为他提出了这个特性。

在 Square，我们使用 Ruby 做很多事情——包括在我们的[*Square Connect Ruby SDK*](https://github.com/square/connect-ruby-sdk#readme)*和我们的* [*开源 Ruby 项目*](https://github.com/square?language=ruby) *。我们热切期待 Ruby 2.6 的发布！*

*想要更多？* [*注册*](https://www.workwithsquare.com/developer-newsletter.html?channel=Online%20Social&sqmethod=Blog) *订阅您的每月开发者简讯或访问我们的* [*dev Slack 频道*](https://squ.re/2Hks3YE) *并说声“嗨！”*