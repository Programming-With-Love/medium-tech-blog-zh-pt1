# 保持匕首锋利⚔️

> 原文：<https://medium.com/square-corner-blog/keeping-the-daggers-sharp-%EF%B8%8F-230b3191c3f?source=collection_archive---------0----------------------->

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

Dagger 2 是一个很好的依赖注入库，但是它的锐利边缘可能很难处理。让我们回顾一下 Square 遵循的一些最佳实践，以防止移动工程师伤害自己！

![](img/cf985b6f5343a6aa599fdb38b7daf804.png)

# 支持构造注入，而不是现场注入

*   字段注入要求字段是非最终的和非私有的。

```
// BAD
class CardConverter { **@Inject** PublicKeyManager publicKeyManager; **@Inject** public CardConverter() {}
}
```

*   在字段上忘记一个`@Inject`会引入一个`NullPointerException`。

```
// BAD
class CardConverter { **@Inject** PublicKeyManager publicKeyManager;
  Analytics analytics; // Oops, forgot to @Inject **@Inject** public CardConverter() {}
}
```

*   构造函数注入更好，因为它允许**不可变的**，因此**线程安全的**对象没有部分构造的状态。

```
// GOOD
class CardConverter { private final PublicKeyManager publicKeyManager; **@Inject** public CardConverter(PublicKeyManager publicKeyManager) {
    this.publicKeyManager = publicKeyManager;
  }
}
```

*   Kotlin 消除了构造函数注入样板文件:

```
class CardConverter
**@Inject** **constructor**(
  private val publicKeyManager: PublicKeyManager
)
```

*   我们仍然对系统构造的对象使用字段注入，比如 Android 活动:

```
public class MainActivity extends Activity { public interface Component {
    **void inject(MainActivity activity);**
  } **@Inject** ToastFactory toastFactory; @Override protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    Component component = SquareApplication.component(this);
    component.**inject**(this);
  }
}
```

# 单身应该是极其罕见的

*   当我们需要对可变状态的集中访问时，单例是有用的。

```
// GOOD **@Singleton**
public class BadgeCounter { public final **Observable<Integer> badgeCount**; @Inject public BadgeCounter(...) {
     badgeCount = ...
  }  
}
```

*   如果一个对象没有可变状态，它不需要是单例的。

```
**//** BAD**,** should not be a singleton! **@Singleton**
class RealToastFactory implements ToastFactory {
  private **final** Application context; @Inject public RealToastFactory(Application context) {
    this.context = context;
  } @Override public Toast makeText(int resId, int duration) {
    return Toast.makeText(context, resId, duration);
  }
}
```

*   在极少数情况下，我们使用作用域来缓存创建成本高的实例，或者重复创建并丢弃的实例。

# 更喜欢“注入”而不是“提供”

*   `@Provides`方法不应该复制构造函数样本。
*   当耦合的关注点在一个地方时，代码更容易理解。

```
@Module
class ToastModule {
  // BAD, remove this binding and add @Inject to RealToastFactory
  **@Provides** RealToastFactory realToastFactory(Application context) {
    return **new RealToastFactory(context)**;
  }
}
```

*   这对**单身族**尤其重要；这是一个关键的**实现细节**，你需要在阅读这个类的时候知道。

```
// GOOD, I have all the details I need in one place. **@Singleton**
public class BadgeCounter { **@Inject** public BadgeCounter(...) {}  
}
```

# 偏爱 static @Provides 方法

*   匕首`@Provides`的方法可以是静态的。

```
@Module
class ToastModule {
  @Provides
  **static** ToastFactory toastFactory(RealToastFactory factory) {
    return factory;
  }
}
```

*   生成的代码可以直接调用该方法，而不必创建模块实例。该方法调用可以由编译器内联。

```
@Generated
public final class DaggerAppComponent extends AppComponent {
  // ... @Override public ToastFactory toastFactory() {
    return **ToastModule.toastFactory**(realToastFactoryProvider.get())
  }
}
```

*   一个静态方法不会改变太多，但是所有绑定都是静态的将会导致相当大的性能提升。
*   使你的模块抽象，如果其中一个`@Provides`方法不是静态的，那么[将在编译时失败](https://github.com/google/dagger/issues/621#issuecomment-321868005)。

```
@Module
**abstract** class ToastModule {
  @Provides
  **static** ToastFactory toastFactory(RealToastFactory factory) {
    return factory;
  }
}
```

# 偏爱“约束”提供

*   当您将一种类型映射到另一种类型时,`@Binds`会替换`@Provides`。

```
@Module
abstract class ToastModule {
  **@Binds**
  **abstract** **ToastFactory** toastFactory(**RealToastFactory** factory);
}
```

*   该方法必须是抽象的。它永远不会被调用；生成的代码将知道直接使用实现。

```
@Generated
public final class DaggerAppComponent extends AppComponent {
  // ... private DaggerAppComponent() {
    // ...
    this.**toastFactoryProvider =** (Provider) **realToastFactoryProvider;**
  } @Override public ToastFactory toastFactory() {
    return toastFactoryProvider.get();
  }
}
```

# 避免在接口绑定上使用@Singleton

> 有状态是一个实现细节

*   只有实现知道他们是否需要确保对可变状态的集中访问。
*   当将一个实现绑定到一个接口时，不应该有任何范围注释。

```
@Module
abstract class ToastModule {
  // BAD, remove @Singleton
  @Binds **@Singleton**
  abstract **ToastFactory** toastFactory(**RealToastFactory** factory);
}
```

# 启用易出错

几个方队正在用它来检测常见的匕首错误，[来看看](https://github.com/google/error-prone)。

# 结论

这些指导原则很适合我们的环境:小型异构团队在大型共享 Android 代码库上工作。由于您的环境可能不同，您应该应用对您的团队最有意义的东西。

## 轮到你了！你遵循什么好的实践来保持你的匕首代码锋利？