# 复杂人工智能任务的端到端模型

> 原文：<https://medium.com/capital-one-tech/end-to-end-models-for-complex-ai-tasks-8c34080145cd?source=collection_archive---------3----------------------->

## 端到端训练的深度神经网络可以胜过经典的基于管道的系统，但它们带来了一些挑战

![](img/145a5099e4ec15d3c6b1a52e9aab4131.png)

***由资深杰出机器学习工程师 Erik Mueller 和数据工程师 Alison Chi***

直到最近，解决复杂人工智能任务(如自然语言对话)的最常见方式是构建基于组件的系统，或由一组组件组成的系统，其中每个组件都被设计为执行任务的一部分。为了降低组件之间交互的复杂性，组件通常被组织为管道，其中每个组件将其输出提供给下一个组件的输入。用于问答的 IBM Watson T5 系统就是这样一个基于组件的系统，被组织成一个流水线。Watson 中的一些组件是基于规则的，另一些是机器学习模型，还有一些是基于规则的系统和机器学习模型的混合。

机器学习相对于传统软件工程的主要优势在于，它允许人们通过从数据中训练模型来构建执行任务的组件，这消除了人类确切指定如何执行任务的需要。下面的问题出现了:为什么不将这个想法发挥到极致，使用机器学习来执行整个复杂的人工智能任务，而不仅仅是这些任务的一部分？端到端模型试图做到这一点，如图 1 所示。

![](img/d2a23b843c2556a001b2f645def9a4b4.png)

Figure 1 End-to-End Models

人们最初认为，自然语言处理任务太难了，无法由端到端模型来执行。例如，在 2008 年， [Punyakanok，Roth 和 Yih](https://www.aclweb.org/anthology/J08-2005/) 认为在更大的任务[语义角色标注](https://en.wikipedia.org/wiki/Semantic_role_labeling)中，一个单独的[句法分析](https://en.wikipedia.org/wiki/Parsing)步骤非常重要。但随后一系列基于[深度神经网络](https://en.wikipedia.org/wiki/Deep_learning#Deep_neural_networks)的端到端模型表现好于预期。在 2011 年， [Collobert 和他的同事](http://www.jmlr.org/papers/volume12/collobert11a/collobert11a.pdf)表明，在几个自然语言处理任务上，端到端模型的性能可以接近基于组件的系统的性能:[词性标注](https://en.wikipedia.org/wiki/Part-of-speech_tagging)、[组块](https://en.wikipedia.org/wiki/Shallow_parsing)、[命名实体识别](https://en.wikipedia.org/wiki/Named-entity_recognition)和语义角色标注。在 2015 年，[周和徐](https://www.aclweb.org/anthology/P15-1109/)提出了一个端到端的语义角色标注模型，该模型优于所有以前的系统，没有使用句法分析。2016 年，[谷歌的端到端神经机器翻译系统](https://arxiv.org/abs/1609.08144)在[机器翻译](https://en.wikipedia.org/wiki/Machine_translation)任务上取得了接近或超过之前所有系统的成绩。并且，在 2017 年， [Lee、He、Lewis 和 Zettlemoyer](https://www.aclweb.org/anthology/D17-1018/) 为[共指消解](https://en.wikipedia.org/wiki/Coreference#Coreference_resolution)引入了一个端到端模型，其性能优于之前所有的系统。

如下表 1 所示，关于端到端模型的论文数量正在增加。

![](img/78800963508d3aefbb83d071d5b8ef3b.png)

Table 1 Papers on End-to-End Models Source: Google Scholar (https://scholar.google.com/) with search string “end-to-end model” (accessed on 3/15/2021)

# 端到端模型的优势和挑战

相对于基于组件的系统，端到端模型有许多优点，但是它们也有一些缺点。

## 端到端模型的优势

*   **更好的指标:**目前，根据[精度和召回](https://en.wikipedia.org/wiki/Precision_and_recall)等指标，性能最好的系统往往是端到端模型。
*   **简单性:**端到端模型避免了确定执行任务需要哪些组件以及这些组件如何交互的棘手问题。在基于组件的系统中，如果一个组件的输出格式改变，其他组件的输入格式可能需要修改。
*   减少工作:端到端模型可以说比基于组件的系统需要更少的工作来创建。基于组件的系统需要更多的设计选择。
*   **对新任务的适用性:**简单地通过使用新数据进行再训练，端到端模型可以潜在地为新任务工作。基于组件的系统可能需要对新任务进行大量的重新设计。
*   **利用自然发生的数据的能力:**端到端模型可以在现有数据上进行训练，例如将作品从一种语言翻译成另一种语言，或者客户服务代理聊天和行动的日志。基于组件的系统可能需要创建新的标记数据来训练每个组件。
*   **优化:**端到端模型针对整个任务进行优化。基于组件的系统的优化是困难的。错误会在组件间累积，一个组件的错误会影响下游组件。下游组件的信息无法通知上游组件。
*   **降低对主题专家的依赖程度:**端到端模型可以在自然发生的数据上进行训练，这减少了对专业语言和领域知识的需求。但通常需要深度神经网络方面的专业知识。
*   **充分利用机器学习的能力:**端到端模型将机器学习的思想发挥到了极致。

## 端到端模型面临的突出挑战

*   **缺乏可解释性:**可能很难理解为什么一个端到端的模型会产生一个给定的结果(尽管对于基于组件的系统来说，情况也是如此)。
*   缺乏可预测性:可能很难预测一个端到端的模型在各种情况下的行为(尽管对于基于组件的系统来说，这种情况也不多见)。
*   **缺乏可诊断性:**在基于组件的系统中，可以识别哪个组件对故障负责，这可以(或不可以)允许修改组件以避免类似的故障，同时在其他情况下保持系统的性能。在端到端系统中，定位故障源可能不那么容易。解决端到端系统中的故障通常涉及修改模型参数、模型架构或训练数据。
*   **数据强度:**端到端模型需要大量数据进行训练。
*   **昂贵的训练和推理:**训练和应用端到端模型可能会消耗大量的计算机能力，甚至是难以处理的。
*   **未知限制:**由于端到端模型相对较新，对其限制还不是很了解，需要对其进行更多的研究。一些应用程序可能不适合端到端模型，可能需要创建基于组件的系统。

# 端到端模型的三个潜在风险

端到端模型存在几个潜在风险:

1.  产生不正确的输出
2.  产生攻击性输出
3.  产生有偏差的输出

这些风险主要来自训练数据的质量和数量，如果不付出巨大努力，很难避免。让我们更详细地讨论它们。

## 风险#1:产生不正确的输出

所有机器学习模型都有产生错误结果的风险。精确度和召回率等标准指标旨在衡量这些模型产生的结果的正确性。然而，[自然语言对话系统存在额外的风险](https://arxiv.org/abs/1711.09050)，因为信息是通过语言传达的。

对话代理有一种信任的因素，这可能使其输出更可信，因此比错误结果以不同形式呈现的风险更大。考虑一个对话式驾驶助手，它告诉司机向右转到一条方向错误的单行道上。如果驾驶员信任助手，并且在采取行动之前不能或没有核实信息，则驾驶员可能最终会听取不正确的信息并采取行动。

## 风险#2:产生攻击性输出

产生不适当或令人不快的输出的风险主要来自训练数据。这方面最突出的例子之一是微软的 Tay T1，这是一个 Twitter 机器人，旨在表现得像 18 至 24 岁的美国千禧女性。微软将实时自适应学习纳入了可能是端到端的模型，允许用户在 Twitter 上与它互动，教它如何行为。在 16 个小时内，Tay 的账户被关闭，因为它发布了大量极具攻击性的推文。将真实的推文和互动作为训练数据是为了不断提高性能。但是，正如[纳吉和内夫](https://ginaneff.com/wp-content/uploads/2016/10/6277-22501-1-PB.pdf)描述的那样，*“这个社会和技术实验失败了，因为泰产生意想不到和非典型行为的能力被用来向她的追随者发送奇怪的，政治上不敏感的，或种族主义和厌恶女性的推文。”*

将产生不良内容的风险降至最低是一项艰巨的任务，消除不良内容的技术有时会适得其反，正如 Tay 的继任者微软的 [Zo](https://en.wikipedia.org/wiki/Zo_(bot)) 所发生的那样。Zo 被设计成模拟一个十几岁的女孩，并在 Messenger、Twitter 和 Skype 等平台上与用户互动。根据 Stuart-Ulin*引用的[微软发言人的说法，“Zo 使用一系列创新方法来识别和生成对话，包括神经网络和长短期记忆(LSTMs)。”](https://qz.com/1340990/microsofts-politically-correct-chat-bot-is-even-worse-than-its-racist-one/)* Zo 旨在避免讨论某些话题。不幸的是，这个[不足以阻止歧视性言论](https://qz.com/1340990/microsofts-politically-correct-chat-bot-is-even-worse-than-its-racist-one/)，Zo 被[关闭](https://twitter.com/zochats?lang=en)。

研究人员正在探索减少针对特定人群的攻击性语言的技术。为了避免种族主义语言， [Schlesinger、Hara 和 Taylor](https://openaccess.city.ac.uk/id/eprint/19124/1/) 建议用*“各种种族言论的数据库，其中种族既有明确的也有隐含的”来扩充训练数据*

## 风险#3:产生有偏差的输出

偏见可以定义为"[对一个人、一个团体、一个想法或一件事情的偏见，尤其是以不公平的方式表达的偏见](https://arxiv.org/abs/1711.09050)"对于机器学习任务来说，隐性偏见可能是难以避免的，因为训练“ [*数据本身往往是社会历史进程的产物，这种进程不利于某些群体*](https://arxiv.org/pdf/1810.08810.pdf)

![](img/18419333774b8a183a2a1d48beddff2f.png)

Table 2 “Results of detecting bias in dialogue datasets.” Source: Ethical Challenges in Data-Driven Dialogue Systems (https://arxiv.org/abs/1711.09050)

偏见通常隐含在[单词嵌入](https://en.wikipedia.org/wiki/Word_embedding)中，这是文本数据的向量表示，对单词出现的上下文进行编码。诸如 [GloVe](https://nlp.stanford.edu/projects/glove/) 和 [Word2Vec](https://www.tensorflow.org/tutorials/text/word2vec) 等文字嵌入技术被发现存在明显的性别偏见。例如，在语义角色标记任务中，*[*在训练集中，活动涉及女性的可能性比涉及男性的可能性高 33%以上，并且经过训练的模型在测试时间*](http://markyatskar.com/publications/bias.pdf) *进一步将差异放大到 68%。”**

*![](img/dafc16493c6921345ea186a2e4fb0a42.png)*

*Table 3 “The most extreme occupations as projected on to the she-he gender direction on g2vNEWS.” Source: Man is to Computer Programmer as Woman is to Homemaker? Debiasing Word Embeddings (https://arxiv.org/abs/1607.06520)*

*但是在去偏置单词嵌入的技术上已经取得了显著的进步。Bolukbasi、Chang、邹、Saligrama 和 Kalai 提出的软硬去偏技术显著减少了*“性别刻板印象，例如接待员和女性之间的联系，同时保持了期望的联系，例如女王和女性之间的联系。”**

# *结论*

*端到端模型是构建人工智能系统的有用技术。由于它们的简单性、性能和数据驱动的性质，应该考虑它们。由于它们的潜在问题，包括不可预测性、偏见和缺乏可解释性，它们可能不适合所有的应用。迄今为止，这些风险可以部分减轻，但不能完全减轻。*

*[*背景矢量*](https://www.freepik.com/vectors/background) *由 starline 创建—*[*www.freepik.com*](http://www.freepik.com)*

*首创者的故事和想法。*

**披露声明:2021 资本一。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。**

**最初发表于*[T5【https://www.capitalone.com】](https://www.capitalone.com/tech/machine-learning/pros-and-cons-of-end-to-end-models/)*。**