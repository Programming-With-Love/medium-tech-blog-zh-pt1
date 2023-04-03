# 用示例探索马尔可夫链——用 Python 开发马尔可夫链

> 原文：<https://medium.com/edureka/introduction-to-markov-chains-c6cb4bcd5723?source=collection_archive---------1----------------------->

![](img/170bb50fff16e37b33d7cf88241f2fc6.png)

Markov Chains — Edureka

你有没有想过谷歌是如何对网页进行排名的？如果你做过研究，那么你一定知道它使用的是基于马尔可夫链思想的 PageRank 算法。这篇关于马氏链介绍的文章将帮助您理解马氏链背后的基本思想，以及如何将它们建模为现实世界问题的解决方案。

以下是本博客将涵盖的主题列表:

1.  什么是马尔可夫链？
2.  什么是马尔可夫性质？
3.  用一个例子理解马尔可夫链
4.  什么是转移矩阵？
5.  Python 中的马尔可夫链
6.  马尔可夫链应用

# 什么是马尔可夫链？

安德烈·马尔科夫在 1906 年首次引入了马尔科夫链。他将马尔可夫链解释为:

***包含随机变量的随机过程，根据一定的假设和确定的概率规则，从一种状态过渡到另一种状态。***

这些随机变量从一种状态转换到另一种状态，这是基于一种叫做**马尔可夫性质的重要数学性质。**

这给我们带来了一个问题:

# 什么是马尔可夫性质？

*离散时间马尔可夫特性表明，随机过程转移到下一个可能状态的计算概率仅取决于当前状态和时间，而与之前的一系列状态无关。*

随机过程的下一个可能的动作/状态不依赖于先前状态的序列，这一事实使得马尔可夫链成为仅依赖于变量的当前状态/动作的无记忆过程。

让我们从数学上推导一下:

设随机过程为，{Xm，m=0,1,2,⋯}.

只有当这个过程是马尔可夫链时，

![](img/3407602ef802b098086e7d73d729a6e4.png)

对于所有的 m，j，I，i0，i1，⋯im-1

对于有限数量的状态，S={0，1，2，⋯，r}，这叫做有限马尔可夫链。

P(Xm+1 = j|Xm = i)这里表示从一个状态转移到另一个状态的转移概率。这里，我们假设转移概率与时间无关。

这意味着 P(Xm+1 = j|Xm = i)不依赖于‘m’的值。因此，我们可以总结如下:

![](img/daeb681630f8bec14de0a7e2962aa85d.png)

这个方程代表了马尔可夫链。

现在我们用一个例子来理解一下到底什么是马尔可夫链。

# 马尔可夫链示例

在我给你一个例子之前，让我们定义一下什么是马尔可夫模型:

## 什么是马尔可夫模型？

*马尔可夫模型是一种随机模型，它以变量遵循马尔可夫特性的方式对随机变量进行建模。*

现在让我们通过一个简单的例子来理解马尔可夫模型是如何工作的。

如前所述，马尔可夫链用于文本生成和自动完成应用程序。对于这个例子，我们将看一看一个示例(随机)句子，看看如何使用马尔可夫链对它建模。

![](img/de845b5c577052e158f5dde186335e6c.png)

上面的句子是我们的例子，我知道它没有多大意义(它不必)，它是一个包含随机单词的句子，其中:

1.  **键**表示句子中的独特单词，即 5 个键(一、二、欢呼、快乐、edureka)
2.  **令牌**表示总字数，即 8 个令牌。

接下来，我们需要了解这些单词的出现频率，下图显示了每个单词以及表示该单词出现频率的数字。

![](img/cc59a1b22cbaf30f72c6cbbb718e077e.png)

左边一栏表示按键，右边一栏表示频率。

从上表中，我们可以得出结论，键“edureka”出现的次数是任何其他键的 4 倍。推断这样的信息是很重要的，因为它可以帮助我们预测在特定的时间点会出现什么单词。如果让我猜例句中的下一个单词，我会选“edureka ”,因为它出现的概率最高。

说到概率，你必须知道的另一个度量是 ***加权分布。***

在我们的例子中,“edureka”的加权分布是 50% (4/8 ),因为在总共 8 个标记中，它的频率是 4。其余的键(一，二，万岁，高兴)都有 1/8 的几率出现(≈ 13%)。

既然我们已经了解了加权分布，并且知道了特定的单词比其他单词出现的频率更高，我们可以继续下一部分了。

![](img/49a5c706d06fb60b57a636b20e65560e.png)

在上图中，我添加了两个额外的单词来表示句子的开始和结束，在下面的部分你会明白我为什么这样做。

现在让我们也为这些键分配频率:

![](img/763a3cae153ee30dccd633eebcef18e6.png)

现在让我们创建一个马尔可夫模型。如前所述，马尔可夫模型用于模拟特定状态下的随机变量，使得这些变量的未来状态仅取决于它们的当前状态，而不是它们的过去状态。

所以基本上在一个马尔可夫模型中，为了预测下一个状态，我们必须只考虑当前状态。

在下面的图表中，你可以看到我们句子中的每个标记是如何指向另一个标记的。这表明未来状态(下一个令牌)是基于当前状态(当前令牌)的。所以这是马尔可夫模型中最基本的规则。

![](img/8cef5064d575a8144fac1a7ccc52f326.png)

下图显示了成对的令牌，其中一对令牌中的每个令牌都指向同一对令牌中的另一个令牌。

![](img/cd6a6acf86d916c7c8e7337383f17419.png)

在下图中，我已经创建了一个结构表示，它显示了每个键和它可以配对的下一个可能的标记的数组。

![](img/3adeaa9b5c84e14bccad31bc0d99ed6a.png)

总结一下这个例子，考虑一个场景，在这个场景中，您必须使用我们在上面的例子中看到的键和标记的数组来组成一个句子。在我们运行这个例子之前，另一个要点是我们需要指定两个初始度量:

1.  初始概率分布(即时间=0 时的开始状态，(‘开始’键))
2.  从一个状态跳到另一个状态的转移概率(在这种情况下，从一个令牌转移到另一个令牌的概率)

我们已经在开始定义了加权分布，所以我们有了概率和初始状态，现在让我们继续例子。

*   所以开始时的初始令牌是[Start]
*   接下来，我们只有一个可能的令牌，即[一]
*   目前，这个句子只有一个词，即“一”
*   从这个记号开始，下一个可能的记号是[edureka]
*   句子更新为“一个 edureka”
*   从[edureka]我们可以移动到以下任何一个令牌[二，欢呼，快乐，结束]
*   有 25%的机会选择“二”,这可能导致形成原始句子(一个 edureka 两个 edureka 欢呼 edureka 快乐 edureka)。但是，如果选择了“end ”,则过程停止，我们将最终生成一个新句子，即“one edureka”。

给自己一个鼓励，因为你刚刚建立了一个马尔可夫模型，并通过它运行了一个测试用例。总结上面的例子，我们基本上是用现在的状态(现在的单词)来确定下一个状态(下一个单词)。这就是马尔可夫过程。

这是一个随机过程，其中随机变量从一种状态转换到另一种状态，使得变量的未来状态仅取决于当前状态。

让我们进入下一步，画出这个例子的马尔可夫模型。

![](img/18793566a4252c7a67b18b914864c8c6.png)

上图被称为状态转换图。我们将在下一节详细讨论这一点，现在只要记住这张图显示了从一个状态到另一个状态的转换和概率。

请注意，图中的每个椭圆形代表一个键，箭头指向可能跟在它后面的键。此外，箭头上的权重表示从/到相应状态转换的概率或加权分布。

这就是马尔可夫模型的工作原理。现在让我们试着理解马尔可夫过程中的一些重要术语。

# 什么是转移概率矩阵？

在上一节中，我们通过一个简单的例子讨论了马尔可夫模型的工作，现在让我们来理解马尔可夫过程中的数学术语。

在马尔可夫过程中，我们用一个矩阵来表示从一个状态到另一个状态的转移概率。这个矩阵被称为转移矩阵或概率矩阵。通常用 p 表示。

![](img/f3fd5e10c05bed6e85a26e553e8c4ea6.png)

注意:pij≥0，所有值' I '为:

![](img/12483cbe76899de24d41e63426976154.png)

我来解释一下。假设我们当前的状态是“我”，下一个或即将到来的状态必须是潜在状态之一。所以在取 k 的所有值求和的同时，一定要得到一个。

# 什么是状态转换图？

马尔可夫模型由状态转移图表示。该图显示了马尔可夫链中不同状态之间的转换。我们用一个例子来理解转移矩阵和状态转移矩阵。

## 转移矩阵示例

考虑具有三个状态 1、2 和 3 以及以下概率的马尔可夫链:

![](img/c90e4d6625f32e52d5c677eeeb1d2bc9.png)![](img/5b05c1cb8e8c77c754b1bfb7f9dda5c4.png)

上图表示马尔可夫链的状态转移图。这里，1、2 和 3 是三种可能的状态，从一种状态指向另一种状态的箭头代表转移概率 pij。当 pij=0 时，意味着状态“I”和状态“j”之间没有转换。

现在我们知道了马尔可夫链背后的数学和逻辑，让我们运行一个简单的演示并理解马尔可夫链可以用在哪里。

# Python 中的马尔可夫链

为了运行这个演示，我将使用 Python。

现在让我们开始编码吧！

## 马尔可夫链文本生成器

**问题陈述:**应用马尔可夫性质，通过学习 Donald Trump 语音数据集，创建能够生成文本模拟的马尔可夫模型。

**数据集描述:**文本文件包含唐纳德·川普(Donald Trump)在 2016 年发表的演讲列表。

**逻辑:**应用马尔可夫属性，通过考虑演讲中使用的每个单词来生成唐纳德的川普的演讲，并为每个单词创建接下来使用的单词的字典。

**第一步:导入需要的包**

```
import numpy as np
```

**第二步:读取数据集**

```
trump = open('C://Users//NeelTemp//Desktop//demos//speeches.txt', encoding='utf8').read()
#display the data

print(trump)

SPEECH 1 ...Thank you so much. That's so nice. Isn't he a great guy. He doesn't get a fair press; he doesn't get it. It's just not fair. 
And I have to tell you I'm here, and very strongly here, because I have great respect for Steve King and have great respect likewise for Citizens 
United, David and everybody, and tremendous resect for the Tea Party. Also, also the people of Iowa. They have something in common. 
Hard-working people....
```

**步骤 3:将数据集分割成单个单词**

```
corpus = trump.split()

#Display the corpus
print(corpus)

'powerful,', 'but', 'not', 'powerful', 'like', 'us.', 'Iran', 'has', 'seeded', 'terror',...
```

接下来，创建一个函数来生成演讲中不同的词对。为了节省空间，我们将使用一个生成器对象。

**步骤 4:创建密钥和后续单词的配对**

```
def make_pairs(corpus):
for i in range(len(corpus) - 1):
yield (corpus[i], corpus[i + 1])
pairs = make_pairs(corpus)
```

接下来，让我们初始化一个空字典来存储单词对。

如果单词对中的第一个单词已经是字典中的一个关键字，只需将下一个潜在单词添加到该单词后面的单词列表中。但是如果这个单词不是一个键，那么在字典中创建一个新的条目，并把这个键赋给这个对中的第一个单词。

**第五步:追加词典**

```
word_dict = {}
for word_1, word_2 in pairs:
if word_1 in word_dict.keys():
word_dict[word_1].append(word_2)
else:
word_dict[word_1] = [word_2]
```

接下来，我们从语料库中随机选取一个单词，这将启动马尔可夫链。

**第六步:建立马尔可夫模型**

```
#randomly pick the first word
first_word = np.random.choice(corpus)

#Pick the first word as a capitalized word so that the picked word is not taken from in between a sentence
while first_word.islower():

#Start the chain from the picked word
chain = [first_word]

#Initialize the number of stimulated words
n_words = 20
```

在第一个单词之后，链中的每个单词都是从特朗普现场演讲中特定单词之后的单词列表中随机抽样的。这显示在下面的代码片段中:

```
for i in range(n_words): chain.append(np.random.choice(word_dict[chain[-1]]))
```

**第七步:预测**

最后，我们来展示一下被刺激的文字。

```
#Join returns the chain as a string
print(' '.join(chain))

The number of incredible people. And Hillary Clinton, has our people, and such great job. And we won’t be beating Obama
```

所以这是我考虑特朗普的演讲得到的生成文本。这可能没有太多意义，但足以让您理解如何使用马尔可夫链来自动生成文本。

现在让我们看看马尔可夫链的更多应用，以及它们是如何被用来解决现实世界的问题的。

# **马尔可夫链应用**

以下是马尔可夫链在现实世界中的应用列表:

1.  整个网络可以被认为是一个马尔可夫模型，其中每个网页都可以是一个状态，这些页面之间的链接或引用可以被认为是具有概率的转移。所以基本上，不管你从哪个网页开始冲浪，到达某个网页的机会，比如说，X 是一个固定的概率。
2.  **打字单词预测:**已知马尔可夫链用于预测即将到来的单词。它们也可以用于自动完成和建议。
3.  **子编辑模拟:**你肯定见过 reddit，并在他们的某个线程或子编辑上有过互动。Reddit 使用了一个 subreddit 模拟器，它消耗了大量的数据，这些数据包含了他们所在小组的所有评论和讨论。通过利用马尔可夫链，模拟器产生字到字的概率，以创建评论和主题。
4.  **文本生成器:**马尔可夫链最常用于生成虚拟文本或产生大型文章和编译演讲。它也用于你在网上看到的名称生成器。

至此，我们结束了对马尔可夫链博客的介绍。如果你想查看更多关于人工智能、DevOps、道德黑客等市场最热门技术的文章，那么你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=introduction-to-markov-chains)

请留意本系列中的其他文章，它们将解释深度学习的各个其他方面。

> 1.[张量流教程](/edureka/tensorflow-tutorial-ba142ae96bca)
> 
> 2. [PyTorch 教程](/edureka/pytorch-tutorial-9971d66f6893)
> 
> 3.[感知器学习算法](/edureka/perceptron-learning-algorithm-d30e8b99b156)
> 
> 4.[神经网络教程](/edureka/neural-network-tutorial-2a46b22394c9)
> 
> 5.[什么是反向传播？](/edureka/backpropagation-bd2cf8fdde81)
> 
> 6.[卷积神经网络](/edureka/convolutional-neural-network-3f2c5b9c4778)
> 
> 7.[胶囊神经网络](/edureka/capsule-networks-d7acd437c9e)
> 
> 8.[递归神经网络](/edureka/recurrent-neural-networks-df945afd7441)
> 
> 9.[自动编码器教程](/edureka/autoencoders-tutorial-cfdcebdefe37)
> 
> 10.[受限玻尔兹曼机教程](/edureka/restricted-boltzmann-machine-tutorial-991ae688c154)
> 
> 11. [PyTorch vs TensorFlow](/edureka/pytorch-vs-tensorflow-252fc6675dd7)
> 
> 12.[用 Python 进行深度学习](/edureka/deep-learning-with-python-2adbf6e9437d)
> 
> 13.[人工智能教程](/edureka/artificial-intelligence-tutorial-4257c66f5bb1)
> 
> 14.[张量流图像分类](/edureka/tensorflow-image-classification-19b63b7bfd95)
> 
> 15.[人工智能应用](/edureka/artificial-intelligence-applications-7b93b91150e3)
> 
> 16.[如何成为一名人工智能工程师？](/edureka/become-artificial-intelligence-engineer-5ac2ede99907)
> 
> 17.[问学习](/edureka/q-learning-592524c3ecfc)
> 
> 18. [Apriori 算法](/edureka/apriori-algorithm-d7cc648d4f1e)
> 
> 19.[tensor flow 中的对象检测](/edureka/tensorflow-object-detection-tutorial-8d6942e73adc)
> 
> 20.[人工智能算法](/edureka/artificial-intelligence-algorithms-fad283a0d8e2)
> 
> 21.[机器学习的最佳笔记本电脑](/edureka/best-laptop-for-machine-learning-a4a5f8ba5b)
> 
> 22.[12 大人工智能工具](/edureka/top-artificial-intelligence-tools-36418e47bf2a)
> 
> 23.[人工智能(AI)面试问题](/edureka/artificial-intelligence-interview-questions-872d85387b19)
> 
> 24. [Theano vs TensorFlow](/edureka/theano-vs-tensorflow-15f30216b3bc)
> 
> 25.[什么是神经网络？](/edureka/what-is-a-neural-network-56ae7338b92d)
> 
> 26.[模式识别](/edureka/pattern-recognition-5e2d30ab68b9)
> 
> 27.[人工智能中的阿尔法贝塔剪枝](/edureka/alpha-beta-pruning-in-ai-b47ee5500f9a)

*原载于 2019 年 7 月 2 日 https://www.edureka.co*[](https://www.edureka.co/blog/introduction-to-markov-chains/)**。**