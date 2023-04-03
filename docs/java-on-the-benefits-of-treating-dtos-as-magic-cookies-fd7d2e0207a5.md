# Java:将 dto 视为神奇饼干的好处

> 原文：<https://medium.com/walmartglobaltech/java-on-the-benefits-of-treating-dtos-as-magic-cookies-fd7d2e0207a5?source=collection_archive---------3----------------------->

对复杂数据处理和健壮 Java 应用程序开发的研究。

![](img/ed4db8565eb2b2cb53c4dc1364a5bb23.png)

My View

[数据传输对象](https://en.wikipedia.org/wiki/Data_transfer_object)(dto)没什么神奇的；在最纯粹的 Java 语言形式中，它们包含一组类型化的数据字段，除了聚合之外没有任何责任。作为 JavaBeans 的最简单形式，DTO 方法由它们的字段的 getters 和 setters 组成，可能还有定制的`[equals](https://docs.oracle.com/javase/9/docs/api/java/lang/Object.html#equals-java.lang.Object-)`、`[hashCode](https://docs.oracle.com/javase/9/docs/api/java/lang/Object.html#hashCode--)`和`[toString](https://docs.oracle.com/javase/9/docs/api/java/lang/Object.html#toString--)`实现。

用计算机科学的术语来说，魔法 cookies 比 dto 更简单:魔法 cookies 是不透明的数据块，没有字段，没有 getters 和 setters。虽然在一种情况下几乎没有意义，但在另一种情况下它们可能很有意义。因此，魔法饼干是一种人格分裂的 DTO:在这里没有意义，在其他地方有很多意义。

# 问题陈述

在分布式应用程序体系结构中，dto 在应用层之间传送信息。由于这些层之间的每个单独通信的资源成本很高(例如，延迟、带宽)，因此生成的 dto 通常包含大量表示复杂信息模型的数据。这些模型在结构上可能是高度分层的，也称为“深度”:例如，包含一个或多个*事物的*集合*和包含更多事物的其他事物的*列表等等。

解决情境化的业务查询(例如，该产品是否可在线交易？)可能需要遍历这些 DTO 层次结构的许多层。并且，给定基于时间的事务的性质，在那些遍历的每个阶段，层级中的下一级可能整体或部分不完整。当在 Java 中工作时，如果不仔细编程，这些遍历会导致运行时异常；例如一个`[NullPointerException](https://docs.oracle.com/javase/9/docs/api/java/lang/NullPointerException.html)`。

单元测试是最小化应用程序实现风险的策略；这些测试需要输入数据，这些数据将测试应用程序代码中的关键路径以及生产环境中可能出现的各种可能的输入。构建和维护这些复杂的 DTO 层级输入很快变得繁琐，并导致复杂的测试设置超过应用代码本身；并且，这可能会掩盖测试结果实际上提供了全面的应用程序代码覆盖的任何保证。

## 示例代码

对象`Alpha`有一个字段`beta`，该字段有一个字段`gamma`，该字段有一个包含描述的`String`字段。这是一个简单的 DTO 等级体系，自然界中存在更复杂的东西。

```
public class Alpha {
    private Beta beta; public Beta getBeta() {
        return beta;
    } public void setBeta(Beta beta) {
        this.beta = beta;
    }
}public class Beta {
    private Gamma gamma; public Gamma getGamma() {
        return gamma;
    } public void setGamma(Gamma gamma) {
        this.gamma = gamma;
    }
}public class Gamma {
    private String description; public String getDescription() {
        return description;
    } public void setDescription(String description) {
        this.description = description;
    }
}
```

给定`alpha`输入对象，访问`description`字段的“安全”或“防弹”Java 代码实现看起来会像这样:

```
if (alpha != null &&
        alpha.getBeta() != null &&
        alpha.getBeta().getGamma() != null &&
        alpha.getBeta().getGamma().getDescription() != null) {
    // ...
}
```

# 一些常见的[痛苦的]单元测试解决方案

访问复杂 DTO 层次结构的单元测试应用程序的常见策略包括模仿测试数据层次结构和组装一个罐装测试数据输入的目录。这两种解决方案在目的上都是成功的，但是在实施和维护方面花费相当大，并且都不能提供真正全面覆盖的保证。

## 模拟测试数据

模拟复杂的 dto 为验证任何应用程序测试场景提供了一种通用的方法。构建这些模拟的 dto 本身可能需要大量的测试设置代码。并且，随着 DTO 层次结构的复杂性增加，测试场景的数量也同样增加。

## 示例代码

```
@Mock
private Alpha alpha;
@Mock
private Beta beta;
@Mock
private Gamma gamma;@BeforeMethod
public void setUp() {
    initMocks(this); doReturn(beta).when(alpha).getBeta();
    doReturn(gamma).when(beta).getGamma();
    doReturn("description").when(gamma.getDescription());
}
```

这涵盖了快乐的路径，但是，人们必须考虑其他场景:`alpha`是`null` , `beta`是`null` , `gamma`是`null`，或者`description`是`null`。

## 罐装测试数据

以序列化形式保存 dto(例如， [JSON](https://www.json.org/) )作为测试输入，限制了 mocking 所需的测试设置代码的数量。相反，这种方法需要识别和保存测试输入，然后长时间维护它们。当数据输入复杂时，这些序列化版本可能会非常大；而且，随着更多的场景被识别，可能需要维护许多副本。

## 示例代码

在`alpha.json`中发现 JSON。

```
{
  "beta": {
    "gamma": {
      "description": "..."
    }
  }
}
```

这又一次涵盖了“快乐之路”的场景。

# 神奇的饼干方法

通过将 dto 视为不透明的东西，即神奇的 cookies，解决了访问数据及其评估的复杂性。以下步骤描述了如何操作。

## 步骤 1:分离关注点:访问与评估

将访问数据的逻辑与评估访问结果的逻辑分开，提供了一种全面验证这两种不同类型的逻辑的方法。然后，访问功能被封装在一个单独的类中，其中的方法在相应的 DTO 类型上实现不同类型的访问。

## 示例代码

一个`AlphaAccess`类对`Alpha`“神奇 cookie”上的访问类型进行建模。是独生子。

```
public class AlphaAccess {
    private static final AlphaAccess INSTANCE = new AlphaAccess(); protected AlphaAccess() {
    } public static AlphaAccess getInstance() {
        return INSTANCE;
    } public Beta getBeta(Alpha alpha) {
        return alpha != null ? alpha.getBeta() : null;
    }
}
```

## 步骤 2:对“安全”访问建模

返回空值的访问类方法不会降低程序员在访问数据时出错的风险。 [Java 8 引入了](http://www.oracle.com/technetwork/articles/java/java8-optional-2175753.html) `[Optional](http://www.oracle.com/technetwork/articles/java/java8-optional-2175753.html)` [类](http://www.oracle.com/technetwork/articles/java/java8-optional-2175753.html)，可以用来消除访问方法上的`null`返回值。

## 示例代码

使用`[Optional](https://docs.oracle.com/javase/8/docs/api/java/util/Optional.html)`返回值实现的`getBeta`方法的更新版本:

```
public Optional<Beta> getBeta(Alpha alpha) {
        return alpha != null ?
                Optional.ofNullable(alpha.getBeta()) :
                Optional.empty();
    }
```

## 步骤 3:重构访问:重磅炸弹超类

访问方法是“go-fer”风格的方法，由于 Java 8 [方法引用](https://docs.oracle.com/javase/tutorial/java/javaOO/methodreferences.html)，可以使用抽象超类来极大地简化访问方法:

## 示例代码

特定 DTO 访问类的抽象超类提供了“获取”字段值的通用实现:

```
public abstract class AbstractAccess<T> { protected <R> Optional<R> get(T o, Function<T, R> getter) {
        return o != null ?
                Optional.ofNullable(getter.apply(o)) :
                Optional.empty();
    }
}
```

然后将`AlphaAccess`子类实现为:

```
public class AlphaAccess extends AbstractAccess<Alpha> {
    ... public Optional<Beta> getBeta(Alpha alpha) {
        return get(alpha, Alpha::getBeta);
    }
}
```

并且，给定一个针对`AbstractAccess`类的独立单元测试，针对`AlphaAccess`类的单元测试也很简单:

```
public class AlphaAccessTest {

    private AlphaAccess alphaAccess;

    @Mock
    private Alpha alpha;
    @Mock
    private Beta beta; @BeforeMethod
    public void setUp() {
        initMocks(this);

        alphaAccess = new AlphaAccess();
    } @Test
    public void testGetBeta() {
        // given
        doReturn(beta).when(alpha).getBeta();

        // when
        Optional<Beta> actual = alphaAccess.getBeta(alpha);

        // then
        assertEquals(actual, Optional.of(beta));
    }
}
```

这为包含在`AlphaAccess`中的访问逻辑提供了 100%的代码覆盖率。

## 步骤 4:委托访问:强制范围

在顶级字段上创建了访问方法后，还可以在顶级创建通过那些下一级对象访问的字段的其他方法，从而简化应用程序访问和验证代码。

## 示例代码

`AlphaAccess`有一个新的注入依赖项`BetaAccess`，它将把对其数据的责任委托给它，`BetaAccess`也有一个注入依赖项`GammaAccess`，它将把对其`description` 字段的访问责任委托给它:

```
private static final AlphaAccess INSTANCE =
        new AlphaAccess(BetaAccess.getInstance());// injected dependency
private final BetaAccess betaAccess;protected AlphaAccess(BetaAccess betaAccess) {
    this.betaAccess = betaAccess;
}...public Optional<String> getDescription(Alpha alpha) {
    return getBeta(alpha).flatMap(betaAccess::getDescription);
}
```

在`BetaAccess`中也有类似的实现:

```
public Optional<String> getDescription(Beta beta) {
    return getGamma(beta).flatMap(gammaAccess::getDescription);
}
```

最后，在`GammaAccess`中，我们有:

```
public Optional<String> getDescription(Gamma gamma) {
    return get(gamma, Gamma::getDescription);
}
```

现在，`AlphaAccess`的单元测试将包括:

```
 @Test
    public void testGetDescription() {
        // given
        doReturn(beta).when(alpha).getBeta();
        doReturn(Optional.of("description")).when(betaAccess)
                .getDescription(beta);

        // when
        Optional<String> actual = alphaAccess.getDescription(alpha);

        // then
        assertEquals(actual, Optional.of("description"));
    }
```

请注意，`betaAccess`是一个类型为`BetaAccess`的模拟对象，在`AlphaAccess`构造时被注入，并在这里被存根化以返回一个`description`字段“描述”`String`值的`Optional`对象。这种存根不是单元测试欺骗，因为这种行为超出了`AlphaAccess`类本身的范围，而`AlphaAccess`类的唯一职责是检索`beta`对象，并将获取包含`description`字段值的`Optional`对象的职责委托给它的访问类`BetaAccess`。

## 第五步:大回报:如果有价值，就做点什么

现在，当一个值存在时，我们的应用程序代码可以安全地实现它的功能，而不需要合并与获取该值相关的所有复杂性。

## 示例代码

`Optional`对象启用`NullPointerException`自由访问代码。

```
public class App {
    // injected dependency
    private final AlphaAccess alphaAccess; private final Alpha alpha; public App(Alpha alpha) {
        this(AlphaAccess.getInstance(), alpha);
    } protected App(AlphaAccess alphaAccess, Alpha alpha) {
        this.alphaAccess = alphaAccess;
        this.alpha = alpha;
    } public void doSomething() {
        alphaAccess.getDescription(alpha).ifPresent(description -> {
            // ...
        });
    }
}
```

并且应用程序单元测试被简化了，因为只有 alpha“魔法 cookie”及其相应的访问类需要被模仿和清除。

```
public class AppTest {
    private App app; @Mock
    private AlphaAccess alphaAccess;
    @Mock
    private Alpha alpha; @BeforeMethod
    public void setUp() {
        initMocks(this); app = new App(alphaAccess, alpha);
    } @Test
    public void testDoSomething() {
        // given
        doReturn(Optional.of("description")).when(alphaAccess)
                .getDescription(alpha);

        // when
        app.doSomething();

        // then ...
    }
}
```

实现 100%的应用程序代码覆盖率更容易实现，因为`AlphaAccess`类封装了如何检索`description`字段值的底层细节；单元测试可以关注结果的评估，而不是构建复杂的 DTO 输入的复杂性。

## 步骤 6: Postscript:过滤结果

将空的`Collection`子类和`Map`实现以及空的`String`对象视为缺失(`Optional.empty()`)可能是有用的。这可以在 Access 超类中轻松完成。

## 示例代码

更新`AbstractAccess`类以过滤出空的`Collection`和`Map`实例以及空白的`String`实例:

```
public abstract class AbstractAccess<T> { protected <R> Optional<R> get(T o, Function<T, R> getter) {
        return o != null ?
                Optional.ofNullable(getter.apply(o))
                        .filter(r -> !(r instanceof String) || 
                            StringUtils.isNotBlank((String) r))
                        .filter(r -> !(r instanceof Collection) ||
                            CollectionUtils
                                .isNotEmpty((Collection<?>) r))
                        .filter(r -> !(r instanceof Map) ||
                            MapUtils.isNotEmpty((Map<?, ?>) r)) :
                Optional.empty();
    }
}
```

## 步骤 7: Postscript:附加集合支持

最后，支持不同的面向`Collection`的过滤器通常很有用。测试一个`Collection`的所有成员是否匹配一个特定的标准(`Predicate`)或任何 do 或 no do，找到一个匹配的或找到第一个匹配的

## 示例代码

再次更新`AbstractAccess`超类，为不同类型的`Stream`导向的访问类型提供支持:`allMatch`、`anyMatch`、`findAny`、`findFirst,`、`noneMatch`。

```
public abstract class AbstractAccess<T> { protected boolean allMatch(Collection<T> collection,
                               Predicate<T> predicate) {
        return isNotEmpty(collection) &&
                collection.stream().allMatch(predicate);
    } protected boolean anyMatch(Collection<T> collection,
                               Predicate<T> predicate) {
        return isNotEmpty(collection) &&
                collection.stream().anyMatch(predicate);
    } protected Optional<T> findAny(Collection<T> collection,
                                  Predicate<T> predicate) {
        return isNotEmpty(collection) ?
                collection.stream().filter(predicate).findAny() :
                Optional.empty();
    } protected Optional<T> findFirst(Collection<T> collection,
                                    Predicate<T> predicate) {
        return isNotEmpty(collection) ?
                collection.stream().filter(predicate).findFirst() :
                Optional.empty();
    } protected <R> Optional<R> get(T o, Function<T, R> getter) {
        // ...
    } protected boolean noneMatch(Collection<T> collection,
                                Predicate<T> predicate) {
        return isNotEmpty(collection) &&
                collection.stream().noneMatch(predicate);
    }
}
```

## 结论:更好的应用

通过将数据访问逻辑从数据评估逻辑中分离出来，我们能够实现更加可靠和可维护的软件。数据模型的复杂性不再掩盖应用程序本身的复杂性。此外，隔离地验证这两种类型的逻辑允许全面检查在访问和评估这两个领域中需要处理的重要场景。

# 下一步:Java 10 扩展

在 Java 10 中，`[Optional](https://docs.oracle.com/javase/10/docs/api/java/util/Optional.html)`类有一些重要的扩展，结合这里描述的访问类策略将非常有用；具体来说，`[ifPresentOrElse](https://docs.oracle.com/javase/10/docs/api/java/util/Optional.html#ifPresentOrElse(java.util.function.Consumer,java.lang.Runnable))`、`[or](https://docs.oracle.com/javase/10/docs/api/java/util/Optional.html#or(java.util.function.Supplier))`和`[stream](https://docs.oracle.com/javase/10/docs/api/java/util/Optional.html#stream())`方法将帮助数据评估代码处理常见场景。

## 示例代码:可选#ifPresentOrElse(消费者，可运行)

某些应用程序代码中，特定值的缺失和它的存在一样重要。

```
alphaAccess.getDescription(alpha).ifPresentOrElse(description -> {
            // do something with the description ...
        }, () -> {
            // do something when there is no description ...
        });
```

## 示例代码:可选#或(供应商)

一些数据场景具有替代的解决策略，这些策略可以封装在访问类本身中。在具有两个字段`description`和`backupDescription`的`Gamma`类的情况下。

```
public class Gamma {
    private String description;
    private String backupDescription; // ...
}
```

如果规则是:当`description`字段为空时，则应使用`backupDescription`字段；然后，`GammaAccess`类可以通过实现以下内容来封装这条规则:

```
 public Optional<String> getDescription(Gamma gamma) {
        return get(gamma, Gamma::getDescription)
                .or(() -> get(gamma, Gamma::getBackupDescription));
    }
```

## 示例代码:可选的#stream()

当处理集合时，我们经常必须处理`Optionals`中的`Stream`，Java 10 通过解引用非空实例来帮助简化:

```
Stream<Optional<Alpha>> alphaStream = ...;
List<String> descriptions =
                alphaStream
                        .flatMap(Optional::stream)
                        .map(alphaAccess::getDescription)
                        .flatMap(Optional::stream)
                        .collect(Collectors.toList());
```

# 附录:抽象访问单元测试

这里是`AbstractAccess`超类的源代码，它为其 helper 方法提供了 100%的代码覆盖率，因此 Access 子类可以在很大程度上专注于它们自己的“快乐之路”场景。

```
import java.util.List;
import java.util.Map;
import java.util.Optional;import org.mockito.Mock;
import org.testng.annotations.BeforeMethod;
import org.testng.annotations.Test;import com.google.common.collect.ImmutableList;
import com.walmart.terrene.TerreneTestCase;import static org.mockito.Mockito.doReturn;
import static org.mockito.MockitoAnnotations.initMocks;
import static org.testng.Assert.assertEquals;
import static org.testng.Assert.assertFalse;
import static org.testng.Assert.assertTrue;public class AbstractAccessTest extends TerreneTestCase { protected interface TestObject {
        List<Object> getList(); Map<Object, Object> getMap(); Object getObject(); String getString();
    }
    private static final String BLANK = "";
    private static final String NOT_BLANK = "not-blank";

    private AbstractAccess<TestObject> abstractAccess;

    @Mock
    private TestObject testObject;
    @Mock
    private Object object;
    @Mock
    private List<Object> objects;
    @Mock
    private Map<Object, Object> objectMap; @BeforeMethod
    public void setUp() {
        initMocks(this); abstractAccess = new AbstractAccess<TestObject>() {
        };
    } @Test
    public void testAllMatch() {
        // then
        assertTrue(abstractAccess.allMatch(
                           ImmutableList.of(testObject),
                           o -> true));
    } @Test
    public void testAllMatchEmpty() {
        // then
        assertFalse(abstractAccess.allMatch(
                        ImmutableList.of(),
                        o -> true));
    } @Test
    public void testAllMatchFalse() {
        // then
        assertFalse(abstractAccess.allMatch(
                        ImmutableList.of(testObject),
                        o -> false));
    } @Test
    public void testAnyMatch() {
        // then
        assertTrue(abstractAccess.anyMatch(
                        ImmutableList.of(testObject),
                        o -> true));
    } @Test
    public void testAnyMatchFalse() {
        // then
        assertFalse(abstractAccess.anyMatch(
                        ImmutableList.of(testObject),
                        o -> false));
    } @Test
    public void testAnyMatchNull() {
        // then
        assertFalse(abstractAccess.anyMatch(
                        null,
                        o -> true));
    } @Test
    public void testFindAny() {
        // then
        assertEquals(abstractAccess.findAny(
                        ImmutableList.of(testObject),
                        o -> true),
                Optional.of(testObject));
    } @Test
    public void testFindAnyEmpty() {
        // then
        assertEquals(abstractAccess.findAny(
                        null,
                        o -> true),
                Optional.empty());
    } @Test
    public void testFindFirst() {
        // then
        assertEquals(abstractAccess.findFirst(
                        ImmutableList.of(testObject),
                        o -> true),
                Optional.of(testObject));
    } @Test
    public void testFindFirstEmpty() {
        // then
        assertEquals(abstractAccess.findFirst(
                        null,
                        o -> true),
                Optional.empty());
    } @Test
    public void testGetBlank() {
        // given
        doReturn(BLANK).when(testObject).getString(); // then
        assertEquals(abstractAccess.get(
                        testObject,
                        TestObject::getString),
                Optional.empty());
    } @Test
    public void testGetEmptyMap() {
        // then
        assertEquals(abstractAccess.get(
                        testObject,
                        TestObject::getMap),
                Optional.empty());
    } @Test
    public void testGetNotBlank() {
        // given
        doReturn(NOT_BLANK).when(testObject).getString(); // then
        assertEquals(abstractAccess.get(
                        testObject,
                        TestObject::getString),
                Optional.of(NOT_BLANK));
    } @Test
    public void testGetNotEmptyList() {
        // given
        doReturn(objects).when(testObject).getList(); // then
        assertEquals(abstractAccess.get(
                        testObject,
                        TestObject::getList),
                Optional.of(objects));
    } @Test
    public void testGetNotEmptyMap() {
        // given
        doReturn(objectMap).when(testObject).getMap(); // then
        assertEquals(abstractAccess.get(
                        testObject,
                        TestObject::getMap),
                Optional.of(objectMap));
    } @Test
    public void testGetNotNull() {
        // given
        doReturn(object).when(testObject).getObject(); // then
        assertEquals(abstractAccess.get(
                        testObject,
                        TestObject::getObject),
                Optional.of(object));
    } @Test
    public void testGetNotNullWhenNull() {
        // then
        assertEquals(abstractAccess.get(
                        null,
                        TestObject::getObject),
                Optional.empty());
    } @Test
    public void testNoneMatch() {
        // then
        assertTrue(abstractAccess.noneMatch(
                        ImmutableList.of(testObject),
                        o -> false));
    } @Test
    public void testNoneMatchEmpty() {
        // then
        assertFalse(abstractAccess.noneMatch(
                        ImmutableList.of(),
                        o -> false));
    } @Test
    public void testNoneMatchFalse() {
        // then
        assertFalse(abstractAccess.noneMatch(
                        ImmutableList.of(testObject),
                        o -> true));
    }
}
```