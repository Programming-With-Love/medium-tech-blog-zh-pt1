# 依赖注入:给你的 iOS 代码打一针强心剂

> 原文：<https://medium.com/square-corner-blog/dependency-injection-give-your-ios-code-a-shot-in-the-arm-ba98594f5002?source=collection_archive---------0----------------------->

## 将依赖注入设计模式应用于 Objective-C 或 Swift 代码库。

*由* [撰写*埃里克·费尔斯通*](https://twitter.com/firetweet) *。*

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

# 什么是依赖注入？

依赖注入(DI)在许多语言中是一种流行的设计模式，如 [Java](http://square.github.io/dagger/) 和 [C#](https://msdn.microsoft.com/en-us/library/vstudio/hh323705%28v=vs.100%29.aspx) ，但在 Objective-C 中还没有得到广泛采用。本文旨在使用 Objective-C 示例简要介绍依赖注入，以及在 Objective-C 代码中使用依赖注入的实用方法。尽管本文关注的是 Objective-C，但所有概念也适用于 Swift。

依赖注入的概念非常简单:一个对象应该要求你传递任何依赖，而不是自己创建它们。马丁·福勒关于这个主题的精彩讨论是非常值得推荐的背景读物。

依赖关系可以通过初始化器(或“构造器”)或属性(或“设置器”)传递给对象。这些通常被称为“构造器注入”和“设置器注入”

构造函数注入:

```
- (**instancetype**)**initWithDependency1:**(Dependency1 *****)d1 
                        **dependency2:**(Dependency2 *****)d2;
```

Setter 注入:

```
**@property** (**nonatomic**, **retain**) Dependency1 *****dependency1;
**@property** (**nonatomic**, **retain**) Dependency2 *****dependency2;
```

正如 Fowler 所描述的，[构造函数注入是首选的](http://martinfowler.com/articles/injection.html#ConstructorVersusSetterInjection)，一般来说，只有在构造函数注入不可能的情况下，才应该使用 setter 注入。通过构造函数注入，您可能仍然拥有这些依赖项的@property 定义，但是您可以将它们设为只读，以简化对象的 API。

# 为什么要使用依赖注入？

依赖注入提供了许多好处，但是一些更重要的好处是:

*   **明确声明依赖关系**对象需要什么来操作变得显而易见，危险的隐藏依赖关系——如全局变量——消失了。
*   复合 DI 鼓励[复合而非继承](http://en.wikipedia.org/wiki/Composition_over_inheritance)，这提高了代码的可重用性。
*   **轻松定制**在创建一个对象时，很容易为特定场景定制对象的某些部分。
*   **明确所有权**特别是在使用构造函数注入时，对象所有权规则被严格执行——有助于构建一个[有向无环对象图](http://en.wikipedia.org/wiki/Directed_acyclic_graph)。
*   最重要的是，依赖注入提高了对象的可测试性。因为它们可以简单地通过填充初始化式来创建，所以不需要管理任何隐藏的依赖关系。此外，模仿依赖关系以将测试集中在被测试的对象上变得很简单。

# 切换到依赖注入

您的代码库可能还没有使用依赖注入设计模式进行设计，但是它很容易上手。依赖注入的一个好的方面是你不需要采用“要么全部要么什么都不要”相反，您可以将它应用到代码库的特定区域，并从那里扩展。

# 要注入的类的类型

首先，让我们将类分为两个桶:基本类和复杂类。基本类是没有依赖关系的类，或者只依赖于其他基本类的类。基本类不太可能被子类化，因为它们的功能是清晰的、不变的，并且不引用外部资源。很多基础类的例子都来自 Cocoa 本身，比如 NSString、NSArray、NSDictionary、NSNumber。

复杂类则相反。它们还有其他复杂的依赖关系，包括应用程序级逻辑(可能需要更改)，或者访问外部资源，如磁盘、网络或全局内存服务。应用程序中的大多数类都很复杂，包括几乎所有的控制器对象和大多数模型对象。许多 Cocoa 类也很复杂，比如 NSURLConnection 或 UIViewController。

给定这些分类，最简单的开始方法是在应用程序中选择一个复杂的类，并在该类中寻找初始化其他复杂对象的位置(搜索“alloc] init”或“new]”)。要将依赖注入引入到类中，请将这个实例化的对象更改为类的初始化参数，而不是实例化对象本身的类。

# 初始化期间分配的依赖关系

让我们看一个例子，其中子对象(依赖项)作为父对象初始化的一部分被初始化。原始代码可能如下所示:

```
**@interface** **RCRaceCar** ()**@property** (**nonatomic**, **readonly**) RCEngine *****engine;**@end** **@implementation** **RCRaceCar**- (**instancetype**)**init**
{
   ... *// Create the engine. Note that it cannot be customized or*
   *// mocked out without modifying the internals of RCRaceCar.*
   _engine **=** [[RCEngine alloc] init]; **return** self;
}@**end**
```

这是一个简单的变化:

```
**@interface** **RCRaceCar** ()**@property** (**nonatomic**, **readonly**) RCEngine *****engine;**@end** **@implementation** **RCRaceCar***// The engine is created before the race car and passed in*
*// as a parameter, and the caller can customize it if desired.*
- (**instancetype**)**initWithEngine:**(RCEngine *****)engine
{
   ... _engine **=** engine; **return** self;
}@**end**
```

# 延迟初始化的依赖项

有些对象在初始化之后才需要，或者根本不需要。一个示例(在使用依赖注入之前)可能如下所示:

```
**@interface** **RCRaceCar** ()**@property** (**nonatomic**) RCEngine *****engine;**@end** **@implementation** **RCRaceCar**- (**instancetype**)**initWithEngine:**(RCEngine *****)engine
{
   ... _engine **=** engine; **return** self;
}- (**void**)**recoverFromCrash**
{
   **if** (self.fire **!=** nil) {
      RCFireExtinguisher *****fireExtinguisher **=** [[RCFireExtinguisher alloc] init];
      [fireExtinguisher extinguishFire:self.fire];
   }
}@**end**
```

在这种情况下，赛车希望永远不会崩溃，我们永远不需要使用我们的灭火器。因为需要这个对象的机会很低，我们不想通过在初始化时立即创建它来减慢每辆赛车的创建。或者，如果我们的赛车需要从多次碰撞中恢复，它将需要创建多个灭火器。对于这些情况，我们可以使用工厂。

工厂是标准的 Objective-C 块，不需要参数，返回对象的具体实例。对象可以控制何时使用这些块创建其依赖项，而不需要知道如何创建它们的细节。

这里有一个使用依赖注入的例子，它使用一个工厂来创建我们的灭火器:

```
**typedef** RCFireExtinguisher *****(**^**RCFireExtinguisherFactory)(); **@interface** **RCRaceCar** ()**@property** (**nonatomic**, **readonly**) RCEngine *****engine;
**@property** (**nonatomic**, **copy**, **readonly**) RCFireExtinguisherFactory fireExtinguisherFactory;**@end** **@implementation** **RCRaceCar**- (**instancetype**)**initWithEngine:**(RCEngine *****)engine
       **fireExtinguisherFactory:**(RCFireExtinguisherFactory)extFactory
{
   ... _engine **=** engine;
   _fireExtinguisherFactory **=** [extFactory **copy**]; **return** self;
}- (**void**)**recoverFromCrash**
{
   **if** (self.fire **!=** nil) {
      RCFireExtinguisher *****fireExtinguisher **=** self.fireExtinguisherFactory();
      [fireExtinguisher extinguishFire:self.fire];
   }
}@**end**
```

在我们需要创建未知数量的依赖项的情况下，工厂也很有用，即使创建是在初始化期间完成的。例如:

```
**@implementation** **RCRaceCar**- (**instancetype**)**initWithEngine:**(RCEngine *****)engine 
                  **transmission:**(RCTransmission *****)transmission
                  **wheelFactory:**(RCWheel *****(**^**)())wheelFactory;
{
   self **=** [super init];
   **if** (self **==** nil) {
      **return** nil;
   } _engine **=** engine;
   _transmission **=** transmission; _leftFrontWheel **=** wheelFactory();
   _leftRearWheel **=** wheelFactory();
   _rightFrontWheel **=** wheelFactory();
   _rightRearWheel **=** wheelFactory(); *// Keep the wheel factory for later in case we need a spare.*
   _wheelFactory **=** [wheelFactory **copy**]; **return** self;
}@**end**
```

# 避免繁琐的配置

如果对象不应该被分配到其他对象中，那么它们被分配到哪里呢？所有这些依赖关系不是很难配置吗？大部分不是每次都一样吗？这些问题的解决方案在于类便利初始化器(think +[NSDictionary dictionary])。我们将把对象图的配置从普通对象中抽出来，留给它们纯粹的、可测试的业务逻辑。

在添加一个类便利初始化器之前，确保它是必要的。如果一个对象的 init 方法只有几个参数，并且这些参数没有合理的默认值，那么就没有必要使用类便利初始化器，调用者应该直接使用标准的 init 方法。

为了配置我们的对象，我们将从四个地方收集依赖关系:

*   **没有合理默认值的值**这些值包括布尔值或数字，这些值在每个实例中可能会有所不同。这些值应该作为参数传递给类便利初始化器。
*   **现有的共享对象**这些应该作为参数(比如 pit 无线电频率)传递给类便利初始化器。这些是以前可能被评估为单例或通过“父”指针评估的对象。
*   **新创建的对象**如果我们的对象不与另一个对象共享这种依赖关系，那么应该在类便利初始化器中新实例化一个 collaborator 对象。这些是以前在对象的实现中直接分配的对象。
*   这些是 Cocoa 提供的单例，可以直接从单例中访问。这适用于像[NSFileManager defaultManager]这样的单例，在这种情况下，可以合理地期望在您的生产应用程序中只使用一个实例。下面有更多关于系统单件的内容。

我们的赛车的类便利初始化器应该是这样的:

```
+ (**instancetype**)**raceCarWithPitRadioFrequency:**(RCRadioFrequency *****)frequency;
{
   RCEngine *****engine **=** [[RCEngine alloc] init];
   RCTransmission *****transmission **=** [[RCTransmission alloc] init]; RCWheel *****(**^**wheelFactory)() **=** **^**{
      **return** [[RCWheel alloc] init];
   }; **return** [[self alloc] initWithEngine:engine 
                          transmission:transmission 
                     pitRadioFrequency:frequency 
                          wheelFactory:wheelFactory];
}
```

你的类便利初始化器应该放在它感觉最合适的地方。一个常用的(和可重用的)配置将存在于相同的。m 文件作为对象，而 Foo 对象专用的配置应该位于@ interface RaceCar(Foo configuration)类别中，并命名为类似 fooRaceCar 的名称。

# 系统单例

对于 Cocoa 中的许多对象，只有一个实例存在。示例包括[ui application shared application]、[NSFileManager defaultManager]、[nsuser defaults standardUserDefaults]和[UIDevice currentDevice]。如果一个对象依赖于这些对象中的一个，那么**它应该作为初始化参数**被包含。即使在您的产品代码中可能只有一个实例，您的测试也可能想要模拟出那个实例，或者为每个测试创建一个实例，以避免测试相互依赖。

建议您避免在自己的代码中创建全局引用的单例，而是在第一次需要时创建一个对象的单个实例，并将其注入到依赖它的所有对象中。

# 不可修改的构造函数

偶尔会有这样的情况，一个类的初始化器/构造器不能被改变或者不能被直接调用。在这些情况下，您应该使用 setter 注入。例如:

```
*// An example where we can't directly call the the initializer.*
RCRaceTrack *****raceTrack **=** [objectYouCantModify createRaceTrack];*// We can still use properties to configure our race track.*
raceTrack.width **=** 10;
raceTrack.numberOfHairpinTurns **=** 2;
```

Setter 注入允许您配置对象，但是它引入了额外的可变性，必须在对象的设计中进行测试和处理。幸运的是，有两种主要情况会导致初始化器不可访问或不可修改，这两种情况都是可以避免的。

# 班级注册

“类注册”工厂模式的使用意味着对象不能修改它们的初始化器。

```
NSArray *****raceCarClasses **=** @[
   [RCFastRaceCar **class**],
   [RCSlowRaceCar **class**],
];NSMutableArray *****raceCars **=** [[NSMutableArray alloc] init];
**for** (**Class** raceCarClass **in** raceCarClasses) {
   *// All race cars must have the same initializer ("init" in this case).*
   *// This means we can't customize different subclasses in different ways.*
   [raceCars addObject:[[raceCarClass alloc] init]];
}
```

一个简单的替代方法是使用工厂块而不是类来声明你的列表。

```
**typedef** RCRaceCar *****(**^**RCRaceCarFactory)();NSArray *****raceCarFactories **=** @[
   **^**{ **return** [[RCFastRaceCar alloc] initWithTopSpeed:200]; },
   **^**{ **return** [[RCSlowRaceCar alloc] initWithLeatherPlushiness:11]; }
];NSMutableArray *****raceCars **=** [[NSMutableArray alloc] init];
**for** (RCRaceCarFactory raceCarFactory **in** raceCarFactories) {
   *// We now no longer care which initializer is being called.*
   [raceCars addObject:raceCarFactory()];
}
```

# 脚本

故事板提供了一种布局用户界面的便捷方式，但是当涉及到依赖注入时，它们也会带来问题。特别是，在故事板中实例化初始视图控制器不允许您选择调用哪个初始化器。类似地，当遵循故事板中定义的序列时，目标视图控制器会为您实例化，而不会让您指定初始化器。

这里的解决方案是避免使用故事板。这似乎是一个极端的解决方案，但是我们发现故事板在大型团队中还有其他问题。此外，没有必要失去故事板的大部分好处。xib 提供了故事板提供的所有好处，除了 segues，但是仍然允许你定制初始化器。

# 公共与私有

依赖注入鼓励你在公共接口中公开更多的对象。如上所述，这有很多好处。但是在构建框架时，它会极大地膨胀您的公共 API。在应用依赖注入之前，公共对象 A 可能已经使用了私有对象 B(私有对象 B 又使用了私有对象 C)，但是对象 B 和 C 从未暴露在框架之外。通过依赖注入，对象 A 在其公共初始化器中有对象 B，而对象 B 又使对象 C 在其初始化器中成为公共的。

```
*// In public ObjectA.h.*
**@interface** **ObjectA**
*// Because the initializer uses a reference to ObjectB we need to*
*// make the Object B header public where we wouldn't have before.*
- (**instancetype**)**initWithObjectB:**(ObjectB *****)objectB;
**@end****@interface** **ObjectB**
*// Same here: we need to expose ObjectC.h.*
- (**instancetype**)**initWithObjectC:**(ObjectC *****)objectC;
**@end****@interface** **ObjectC**
- (**instancetype**)**init**;
**@end**
```

对象 B 和对象 C 是实现细节，您不希望框架消费者必须担心它们。我们可以通过协议来解决这个问题。

```
**@interface** **ObjectA**
- (**instancetype**)**initWithObjectB:**(**id** **<**ObjectB**>**)objectB;
**@end***// This protocol exposes only the parts of the original ObjectB that*
*// are needed by ObjectA. We're not creating a hard dependency on*
*// our concrete ObjectB (or ObjectC) implementation.*
**@protocol** **ObjectB**
- (**void**)**methodNeededByObjectA**;
**@end**
```

# 关闭

依赖注入是 Objective-C(以及 Swift 的扩展)的天然选择。如果应用得当，它会让你的代码易于阅读、易于测试和维护。

[](https://twitter.com/firetweet) [## 埃里克·费尔斯通(@firetweet) |推特

### 埃里克·费尔斯通的最新推文(@firetweet):“祝贺@segiddins 和@CocoaPods 团队达到 1.0…

twitter.com](https://twitter.com/firetweet)