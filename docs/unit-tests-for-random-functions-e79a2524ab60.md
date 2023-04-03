# 随机函数的单元测试

> 原文：<https://medium.com/javascript-scene/unit-tests-for-random-functions-e79a2524ab60?source=collection_archive---------1----------------------->

## 猎枪后置第 3 集

![](img/991d0b9dbb03bab579fb88c41f2f5453.png)

Photo: [Brandon Bailey](https://www.flickr.com/photos/luxurydesigner/) — Midnight Cowboy (CC-BY-2.0)

shotgun 是一个视频系列，当我为真正的应用和库解决真正的编程挑战时，你可以和我一起乘坐 Shotgun。这些视频只对 EricElliottJS.com[](https://ericelliottjs.com/product/lifetime-access-pass/)**的成员开放，但我在这里记录这些冒险。**

*当你使用 TDD 足够长的时间时，你将面临的挑战之一是这个问题:“我如何测试随机函数？”*

*在 Shotgun 系列的这一集中，我们遇到了这个问题，因为我们想要生成一个随机的时间表来发送自动化的社交媒体更新。*

*如果我们想要富有成效，那么珍惜我们的时间是很重要的，所以 [PostAmp](https://mypostamp.com/) 允许我们一次批量创建一堆社交媒体帖子是很好的，但我们不想每周一次向我们的粉丝大量发送 30 条推文。人们会很快不关注你，因为你破坏了他们的通知。*

*相反，如果我们可以将它们全部分批，PostAmp 可以生成一个随机的时间表，在一周内为我们发布它们，这不是很好吗？*

*但是要做到这一点，我们需要写一些函数，来产生随机输出。*

*纯函数是最容易进行单元测试的函数。只需调用它们，然后断言输出是您所期望的。*

*但是随机输出的函数呢？我们不能断言输出是我们所期望的。相反，我们需要断言输出服从一些约束。*

*生成随机发布时间表过程的第一步，我们需要分解问题。让我们从生成一个给定范围内的随机数开始。签名将如下所示:*

```
*randomNumber = (start: Number, end: Number) => Number*
```

*我们的约束非常简单:*

*   *给定没有开始和结束，生成一个大于或等于开始的随机数*
*   *给定开始和结束，生成一个小于或等于结束的随机数*

*当我们处理随机数时，我们希望确保不仅仅是单个数字符合我们的约束。我们需要相当程度的确定性，所有生成的数字都将符合我们的约束。*

*为了做到这一点，我通常会生成一大堆，然后全部测试。这是我想到的:*

```
*import { describe } from 'riteway';
import { randomNumber } from './utils.js';describe('randomNumber', async assert => {
  const start = 3;
  const end = 20;
  const numbers = Array.from({ length: 100 }, () => randomNumber(start, end));assert({
    given: 'start, end',
    should: 'generate a random number greater than or equal to start',
    actual: numbers.every(n => n >= start),
    expected: true
  });assert({
    given: 'start, end',
    should: 'generate a random number less than or equal to end',
    actual: numbers.every(n => n <= end),
    expected: true
  });
});*
```

# *首先，写一个测试，让它通过*

*如果你看了视频，你可能会注意到一些事情。首先，我的测试描述是错误的。我说，“假定没有争论”——我已经在这篇博文中更正了。哎呀。*

*但是你也会注意到我一次只写了一个测试。我开始说:*

```
*assert({
  given: 'start, end',
  should: 'generate a random number greater than or equal to start',
  actual: numbers.every(n => n >= start),
  expected: true
});*
```

*在进行下一个测试之前，我看到这个测试失败了，然后编写了实现:*

```
*export const randomNumber = (start, end) =>
  Math.round(Math.random() * (end - start) + start);*
```

*然后我写了下一个测试:*

```
*assert({
  given: 'start, end',
  should: 'generate a random number less than or equal to end',
  actual: numbers.every(n => n <= end),
  expected: true
});*
```

*下一个测试已经通过了，因为我写了整个实现。有时候，如果我知道我希望实现是什么样的，我会写出来，然后做一个改变来打破测试，然后看着测试失败，然后修复代码，看着它再次通过。*

*我为什么要这么做？去测试测试！*

> *如果你没有见过测试失败，你不知道测试是否有效。*

*如果你看了视频，你也会注意到我在最后犯了一个愚蠢的错误，当我试图破坏`end`测试时，我像这样修改了代码:*

```
*export const randomNumber = (start, end) =>
  Math.round(Math.random() * (5 - start) + start);*
```

*当然，这并没有破坏测试，因为`5`仍然在有效范围内。为了找出这个问题，我将实际的输出数组记录到控制台中。你们中比较聪明的人可能已经猜到我故意这么做有两个原因:*

1.  *我想展示测试调试技术*
2.  *我想让你知道*每个人*都会犯错。*

*猜得好！但是那是一个真正的错误，也是一个特别愚蠢的错误。我们得到那些额外的课程只是一个幸运的意外。记住，每个人都会犯错。如果没有，我们就不需要 TDD 了。*

*TDD 就像一个复式记账分类账。对于每一个变更，您都有两个变更记录:在代码中，以及在您的测试中。如果你能让两个记录一致，你就有双倍的信心你写的代码是正确的。*

# *后续步骤*

*会员可以在 EricElliottJS.com[上](https://ericelliottjs.com)[观看新一集](https://ericelliottjs.com/shotgun-postamp-episode-3-testing-random-outputs)。如果你不是会员，现在是一个伟大的时间来看看你错过了什么！*

*[![](img/1477d1002811845a63c46835ce48f127.png)](https://ericelliottjs.com/premium-content/lesson-pure-functions)*

*[开始您的 EricElliottJS.com 免费课程](https://ericelliottjs.com/premium-content/lesson-pure-functions)*

****艾里克·艾略特*** *著有《书籍》、* [*【排版软件】*](https://leanpub.com/composingsoftware)*[*【编程 JavaScript 应用】*](https://www.amazon.com/Programming-JavaScript-Applications-Architecture-Libraries-dp-1491950293/dp/1491950293/ref=as_li_ss_tl?_encoding=UTF8&language=en_US&linkCode=ll1&linkId=06971c7a0f2b13309e5af242b2483609&me=&qid=&tag=eejs-20) *。作为*[*【EricElliottJS.com】*](https://ericelliottjs.com/)*和*[*devanywhere . io*](https://devanywhere.io/)*的联合创始人，他教授开发者必备的软件开发技能。他为加密项目组建开发团队并提供建议，为 Adobe Systems、* ***、Zumba Fitness、*** ***【华尔街日报、*******【ESPN、*******BBC、*** *以及包括* ***Usher、弗兰克·奥申、金属乐队在内的顶级录音*******

**他和世界上最美丽的女人享受着与世隔绝的生活方式。**