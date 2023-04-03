# 探索 Ruby 2.6:枚举器、哈希和可枚举的变化

> 原文：<https://medium.com/square-corner-blog/exploring-ruby-2-6-enumerator-hash-and-enumerable-changes-11ffa4b12eb9?source=collection_archive---------0----------------------->

## 即将发布的 ruby-2.6.0-preview3 中尝试的新特性

![](img/507aad639b708915200d2ccc67274543.png)

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

随着圣诞节的临近，我们将会看到越来越多的 Ruby 2.6 特性。其中有些讨论的不多，包括 Enumerable 和 Enumerator，我发现那些变化特别有意思。让我们来看看。

![](img/2284fa8f62759289509fd9434aaf135a.png)

Christmas is coming, and with it some fun new presents in Ruby!

# 可枚举的#to_h

如果你在早期使用过 Ruby，你可能会对`Hash[]`和它的用法很熟悉:

```
hash = { a: 1, b: 2 }
Hash[hash.map { |k, v| [k, v + 1] }]
=> { a: 2, b: 3 }
```

在 2.x 天，我们开始看到`Enumerable#to_h`:

```
hash = { a: 1, b: 2 }
hash.map { |k, v| [k, v + 1] }.to_h
=> { a: 2, b: 3 }
```

这当然是一个更好的选择，但如果它更进一步呢？在 Ruby 2.6+ `to_h`中会带一个功能相当类似 map 的块:

```
hash = { a: 1, b: 2 }
hash.to_h { |k, v| [k, v + 1] }
=> { a: 2, b: 3 }
```

因为这是一个关于 Enumerable 的方法，所以我们也可以把它用在其他事情上，比如范围和数组:

```
(1..5).to_h { |n| [n, n**2] }
=> {1=>1, 2=>4, 3=>9, 4=>16, 5=>25}
```

诚然，它不像`map`和`to_h`的组合那样明确，但它确实给了我们多一步来减少哈希转换代码。

# 哈希#合并

在 Ruby 的早期版本中，为了将多个散列合并在一起，您必须一次合并一个:

```
h1.merge(h2).merge(h3).merge(h4)
```

…或者可能使用 double-splat 将它们都视为一个大的内联散列:

```
h1.merge(**h2, **h3, **h4)
```

…或者对于我们这些特别喜欢 reduce 的人来说，甚至有一种 Ruby 的味道！

```
[h1, h2, h3, h4].reduce(&:merge)
```

现在这些都工作了，但是有变化，这就是问题所在。Ruby 2.6 为`Hash#merge`引入了可变参数:

```
h1.merge(h2, h3, h4)
```

请注意，这也适用于`Hash#merge!`和`Hash#update`。

# 枚举器::算术序列

这个词很拗口——试着把它说五遍。如果你还没有打开你的 REPL，那就打开吧。我们要尝试一些事情。

在 2.6 之前，如果你尝试这样做:

```
(1..100).step(3) == (1..100).step(3)
```

你会得到一个很好的机会。他们看起来一样，对吗？为什么不平等？

结果核心团队同意了，所以他们创造了算术序列的概念来处理这个问题:

[](https://bugs.ruby-lang.org/issues/13904) [## 特性#13904:获取枚举器的原始信息- Ruby 主干- Ruby 问题跟踪…

### 红矿

bugs.ruby-lang.org](https://bugs.ruby-lang.org/issues/13904) 

最大的问题是普查员没有真正的关于他们自己的内省信息，这意味着他们不太知道如何相互比较。

作为一个额外的好处，我们有更多的东西可以玩。主要是关键字，以使我们在创建步进枚举器时的意图更加明确:

```
(1..10).step(3) == 1.step(by: 3, to: 10)1.step(by: 2, to: 10)
=> [1, 3, 5, 7, 9](1..10).step(3)
=> [1, 4, 7]
```

我们还发现了模运算符的一个新用途:

```
(1..100).step(3) == (1..100) % 3
```

…尽管公平地说，我敢打赌，这一条将会比实际生产代码更频繁地用于 code golf。

# 所有人现在在一起！

如果没有一个不可思议的、坦率地说是可怕的代码示例，这就不是一篇文章了。别害怕！我们得到了我们的老朋友 FizzBuzz:

```
def fizzbuzz(rules, range)
  mappings = rules.map { |step, value|
    (range % step).to_h { |n| [n, value || n] }
  } fizzbuzz_map = {}.merge(*mappings) { |_, old_value, new_value|
    is_numeric = [old_value, new_value].any?(Numeric)
    is_numeric ? new_value : old_value + new_value
  } fizzbuzz_map.values
endrules = {1 => nil, 3 => 'Fizz', 5 => 'Buzz'}
range = (1..100)puts fizzbuzz(rules, range)
```

我们在这里所做的是定义一个规则集，它使用一个散列来表示步进率(从范围中的最后一个数字开始要跳过多少个数字)和一个值。

因为 3 和 5 的倍数有特殊的映射，所以我们在规则中这样说。这意味着当我们开始创建步长值时，它不会传递给语句`value || n`中的实际数值。

我们的第一步是在给定的范围内转换每一个规则集，只选择能被给定步进率整除的数字。

关于`merge`的一个有趣的事情是，它可以使用一个块来公开这个键，以及在两个不同的哈希表中这个键的交叉值作为旧值和新值。利用这一点，我们可以决定“保留”哪个值。

我们总是假设字符串规则优先于基于数字的规则，如果已经有一个字符串，我们一定在那里找到了一个可以被 15 整除的数字。

现在，这肯定是低效的，但仍然是一些新功能的有趣探索。

# 包扎

2.6 中有很多有趣的新特性，其中很多甚至还没有出来。这只是一小部分，我们期待着看到其余的部分。

感谢我的同事 Square Shannon“haven wood”Skipper 和我一起探索 ruby-2.6.0-preview3 的最新 Ruby 提交。我们在 Square 使用了大量的 Ruby——在我们的 Square Connect Ruby SDKs、我们维护的许多开源项目等等。我们热切期待 Ruby 2.6 的发布！