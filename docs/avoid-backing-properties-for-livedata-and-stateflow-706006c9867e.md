# 避免支持 LiveData 和 StateFlow 的属性

> 原文：<https://medium.com/google-developer-experts/avoid-backing-properties-for-livedata-and-stateflow-706006c9867e?source=collection_archive---------0----------------------->

![](img/65e785f18de90f05e608fc998243252e.png)

[https://unsplash.com/photos/OopPIi_A428](https://unsplash.com/photos/OopPIi_A428)

如果你曾经使用过`[LiveData](https://developer.android.com/topic/libraries/architecture/livedata)`,你可能已经写了类似这样的代码:

```
class MyViewModel: ViewModel() { **val loading: LiveData<Boolean>
       get() = _loading** **private val _loading = MutableLiveData<Boolean>()**}
```

这似乎是当今开发人员公开一些不可变的`LiveData`的典型方式，同时能够在我们将数据写入的实现中有一个可变的版本。

每当我看到，甚至不得不写下这种代码时，我内心就有些畏缩。正如我在[的一次演讲](https://speakerdeck.com/dpreussler/take-your-kotlin-to-the-next-step-abandon-what-youve-learned-in-java)中引用的，我们大脑中的这种感觉是真实的:

> [*社交失策激活大脑中的区域，[..]以前与身体疼痛有关。*](https://sciencephenomena.wordpress.com/2015/05/10/the-neurological-aspect-of-cringing/)

作为开发人员，我们知道这段代码有问题，对吗？这也感觉像我们在这里写手工 getters 和 setters。

# 怎么了?

我们可以从我们用于后台字段的前缀开始，尽管我们努力了很长时间来摆脱前缀，但我们在这里接受它！它甚至被写进了官方的编码惯例。

但即使我们给它重新命名，它仍然畏缩不前:

```
class MyViewModel: ViewModel() { val **loading**: LiveData<Boolean>
       get() = **mutableLoading** private val **mutableLoading** = MutableLiveData<Boolean>()}
```

这种重复感觉没必要！特别是如果你写了一些类似于 ViewModel 的东西，暴露了很多这样的问题，你会迷失在阅读这些重复的代码中。

# 但只是 LiveData？

你可能会认为这只是`LiveData`的专长，而这种结构的未来可能会更加有限。

你就不会有这个问题。该语言通过一个私有 setter 支持这一点:

```
var secret: String = "Secret"
  ** private set**
```

但是镇上来了一个新的小孩:`StateFlow`需要同样的东西！请看来自 Jetbrains 官方博客[的片段:](https://blog.jetbrains.com/kotlin/2020/10/kotlinx-coroutines-1-4-0-introducing-stateflow-and-sharedflow/)

```
class DownloadingModel { **private val _state** = MutableStateFlow<DownloadStatus>(DownloadStatus.NOT_REQUESTED) **val state: StateFlow<DownloadStatus> get() = _state**
```

这个问题讨论很多。有些人甚至建议改变科特林语来支持这一点！

尽管这感觉就像我们想用大锤砸坚果或用大炮射麻雀或你们语言中的任何术语！

难道没有更微妙的东西来解决这个问题吗？

# 我们的实际目标是什么？

我们希望将公共 API 从实际实现中分离出来，对吗？

信不信由你，我们已经有了这方面的语言结构！叫**接口**！

尽管许多开发人员会定期为类似于`Repositories`的类提取接口，但很少有人为`ViewModels`提取接口。

这是为什么呢？因为只有一个实现？也许视图模型是视图之前的最后一块积木，我们几乎不需要在测试中模拟它们？
也许！但我们刚刚找到了另一个使用它们的好理由。

对于`ViewModel`来说，抽象类会更容易，因为我们无论如何都需要扩展 Google`ViewModel`(如果你愿意，你可以使用接口)

```
abstract class MyViewModel: ViewModel() {
 **abstract val loading: LiveData<Boolean>**
}class MyViewModelImpl: MyViewModel() {
 **override val loading = MutableLiveData<Boolean>()**
}
```

从 ViewMode 里面已经现了类型:
`override val loading: **MutableLiveData<Boolean>**`

这被称为[协变返回类型](https://en.wikipedia.org/wiki/Covariant_return_type#:~:text=In%20object%2Doriented%20programming%2C%20a,is%20overridden%20in%20a%20subclass.&text=This%20usually%20implies%20that%20the,type%20of%20the%20overridden%20method.)，其中实现返回比原始声明更具体的类型。现在好的一点是，我们可以从我们的实现中直接为我们的加载状态设置值:

```
class MyViewModelImpl: MyViewModel() {
override val loading = **MutableLiveData**<Boolean>() fun doSomeWork() {
     // ...
 **loading.value = true**  }
}
```

# 不是泄露细节吗？

您可能会问，我们是否没有公开太多的实现细节？如果你直接使用`MyViewModelImpl`(而不是抽象类)，你可以从外部写入`loading`！

答案是**不**！

无论如何，除了创建这个类的人之外，没有人应该知道这件事！通常这是我们进行依赖注入的层，或者在`ViewModels`的情况下，是`ViewModelFactory`。尽量避免直接看到或创建实现。

当然，有人总是可以将接口向上转换为具体的类，但是也可以向上转换原始的`loading`！

这里引用一个老的 clean coders 博客:

> ***谁会这么做？*** *我不知道。呃。坏人。*
> 
> ***你团队里有坏人吗？*** *没有但是。这感觉不安全。*
> 
> 嗯，如果这是公共 API 的一部分，我同意你的观点。但是如果这只是我们团队使用的代码，那么…

如果那些只是我们的同事，我们就不需要保护我们的代码免受不良行为者的攻击。我们的工作是确保 API 是清晰和干净的，所以用法是可以理解的！专注于防止错误，而不是滥用。

# 谷歌视图模型的细节

Jetpack 的视图模型有一些细节，让我们简单讨论一下在哪里使用抽象类。

如果你用 Koin 它的琐碎:

```
private valviewModel: **MyViewModel by *viewModel*()**
```

在您的模块中，您应该这样声明它:

```
viewModel**<MyViewModel>** { **MyViewModelImpl**(...) }
```

当使用来自 Ktx 的代理时，事情有时会更棘手一些(比如测试)。
如果您使用自定义提供程序，这很容易，例如可以通过 Dagger 注入:

```
@Inject lateinit var provider: **MyViewModelProvider**
private val viewModel: **MyViewModel by *viewModels*** { provider }
```

如果使用通用工厂，这就不那么明显了:

```
@Inject lateinit var factory: **ViewModelProvider.Factory**
private val viewModel: **MyViewModel by *viewModels***{ factory }
```

你必须以某种方式告诉 Dagger 如何进行匹配。但是应该在你的模块中以正常的方式完成，你可以手动或者用`@Bind`将`MyViewModel`绑定到`MyViewModelImpl`。这意味着更多的 Dagger 代码但 ViewModel 中的代码更少，这应该是值得的。当谈到 Dagger 时，有时如果它改善了你的整体架构，那么越多越好。专注于改进你每天阅读和工作的代码，而不是幕后的匕首设置。

# 结论

通过使用接口或抽象类，您不仅可以消除实现中的所有重复，还可以专注于功能。减少噪音对于干净和可重用的代码非常重要。

另外，你为你的用户创建了一个非常干净的 API，这里有一个来自我正在做的一个小测验应用，使用 Flow:

```
abstract class GameViewModel: ViewModel() {
    val nextQuestion: Flow<String>
    val nextAnswers: Flow<Answers>
    val loadingVisible: Flow<Boolean>
    val score: Flow<String>
    val gameOver: Flow<Boolean>
}
```

如果你回想一下用头文件编程的 C 语言，这就是我们当时的情况。清晰的对外接口，完美的封装。
一门被遗忘的艺术有时……

PS:所有的荣誉都归于艾丹·麦克威廉姆斯，是他让我注意到了这一点！