# Java 中可选的真正力量

> 原文：<https://medium.com/walmartglobaltech/true-power-of-optional-in-java-306a77c62d00?source=collection_archive---------0----------------------->

![](img/ad958e632ce16eabbdec2282320463d3.png)

Photo by [Emile Perron](https://unsplash.com/@emilep?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

在 Java 中可选的是一个对象的容器，它可能包含也可能不包含值。这是 Java 工程师很少理解的一个事实，他们使用 Optional 作为执行空检查和避免 NullPointerException 场景的“奇特方式”。

> null——十亿美元的错误？

null 是一个特殊的值，在 Java 中可以作为文字使用，可以用来表示无值。它的行为对于它被赋给的任何引用类型都是相同的和中立的。null 的概念是由英国计算机科学家东尼·霍尔在 20 世纪 60 年代初提出的，但他后来创造了他的十亿美元的错误，因为在某种程度上，它为 C 语言的 get 实用程序首次引入的恶意软件的创建奠定了基础。因此，现代编程语言用快速失效方法处理 null 要有效得多。

> 但是用老方法执行空检查有什么问题呢？

在我看来——在需要的地方添加空检查没有错。Java 8 中添加的新的可选类并不是要取代每一个空引用。还要注意，Optional 的不当使用会在代码中引入 NullPointerException，我们将在后面的部分中看到这一点。

记住，Optional 是一个对象的容器，它可能包含也可能不包含值。这基本上意味着，可选的概念用于表示特定的实体或对象可以包含值，也可以不包含值。因此， **Optional 给出了区分缺少值和空引用的能力。**因此，Null 可用于指示在该点之前程序流程中出现的错误。

让我用一个例子来说明这一点:

```
Optional<String> name = Optional.*of*("John Smith"); /* with a valid value */
```

这用提供的值声明了一个可选容器。或者，您也可以使用 *empty()* 方法来表示容器中没有值，如下所示。

```
Optional<String> name = Optional.*empty*(); /* shows the absence of a value */
```

Optional 类还提供了一个方法 *ofNullable()* 来创建带有空值的可选容器。

```
Optional<String> name = Optional.*ofNullable*(null); /* can be a null value */
```

假设您需要在名称存在时打印名称，在名称不存在时打印默认值。您可以使用传统的空值检查来实现这一点:

```
String patronName = .... ; /* may or may not be null */
if(patronName != null) {
    System.*out*.println(patronName);
} else {
    System.*out*.println("Anonymous");
}
```

同样，也可以使用可选的实现，如下所示:

```
Optional<String> patronName = Optional.*ofNullable*("John Smith");
System.*out*.println(patronName.orElse("anonymous"));
```

在这种情况下，如果*父名*包含一个非空值，将打印该值，否则，将打印字符串‘Anonymous’。

> 这难道不比 null-check 方式更简洁吗？

您可以在 web 上找到更多使用 Optional 的例子，所以让我在这里重点介绍一些使用 Optional 的反模式。

## 1)使用可选的而不是普通的空检查。

示例:不必要的包装为可选的，而不是普通的空检查。

```
if (Optional.*ofNullable*(customer.getMiddleName()).isPresent())
```

代替

```
if (customer.getMiddleName()!= null) /* one can also use the StringUtils class methods for a wider range of validations. */
```

另一个例子:使用 *isPresent()* 的嵌套空检查

```
if (Optional.*ofNullable*(customer).isPresent() && Optional.*ofNullable*(customer.getPhoneNumber()).isPresent()) { savePhoneNumber(customer.getPhoneNumber());....}
```

## 2)is present()—get()模式

在代码审查过程中，我经常看到人们结合使用 *ifPresent()* 和 *get()* 方法来安全地从可选对象中获取值。虽然这在 Java 中是合法的，但这是对 Optional 的低效使用，这通常是由于对我们为什么使用 Optional 缺乏深入的理解。例如:

```
if(patronName.isPresent()){ String fullName = patronName.get();....}
```

您可以使用普通的空支票来代替 *isPresent()-get()* 组合。

## 3)使用 isPresent()-get()而不是 ifPresent()

*ifPresent()* 方法是一种基于值的存在来执行操作的强大方法。然而，这一点有时会被误用的 *isPresent()-get()* 组合所忽略。

例如:使用下面的代码片段

```
List<CustomerAddress> addresses = new ArrayList<>();
if (customerAddressOptional.isPresent()) {
    addresses.add(customerAddressOptional.get());
....
}
```

而不是下面简洁的片段:

```
customerAddressOptional.ifPresent(addresses::add);
```

现在，这可能乍一看很明显，但是当许多年轻的 Java 工程师习惯了 if-else 范式时，他们会陷入一个常见的陷阱。

## 4)必要时使用 orElse()而不是 orElseGet()

请记住， *orElse()* 方法总是执行它的参数，而 *orElseGet()* 将一个供应商作为它的参数，并且只在可选参数为空时执行。理解它们之间的区别可能会改变游戏规则，特别是当正在执行的操作是资源密集型的，比如 web 服务调用、数据库查询等。

示例:假设我们有一个 *getDefaultValue()* 方法，定义如下:

```
public String getDefaultValue() {
 System.*out*.println(“Inside getDefaultValue() method”);
 return “Anonymous”;
 }
```

让我们在这里看看 *orElse()* 和 *orElseGet()* 的行为:

```
/* getDefaultValue() is always executed in this case. */
Optional<String> patronName = Optional.*of*("John Smith");
System.*out*.println(patronName.orElse(getDefaultValue()));/* getDefaultValue() is not executed since patronName is present. */
Optional<String> patronName = Optional.*of*(“John Smith”);
System.*out*.println(patronName.orElseGet(() -> *getDefaultValue*()));
```

在执行上面的代码片段时，您可以看到，当与 *orElse()* 一起使用时，无论 *patronName* 是否存在，都会打印出“*Inside get default value()method”*。

## 5)没有利用 FlatMap 来检查嵌套的可选对象。

例如:

```
Customer customer = null;String lastname = Optional.*ofNullable*(customer)
.flatMap(c -> c.lastname)
.orElse(“Not available”);System.*out*.println(lastname);
```

## 6)将方法的参数声明为可选的

虽然严格来说这不是一个坏主意，但它可能会导致调用者端不必要的复杂代码。使用重载方法来指定非强制参数要容易得多。

示例:

```
public void findNewCustomers(String city, Optional<Integer> numberOfOrders) {
 List<Customer> allCustomers = .... ;
 allCustomers.stream()
 .filter(customer -> customer.city.equals(city))
 .filter(customer -> customer.numOrders >=numberOfOrders.orElse(0))
 .collect(Collectors.*toList*());
 }
```

这种方法的问题是，可以通过将 *null* 作为第二个参数来调用 *findNewCustomer()* 方法，这将导致在*number of orders . or else(0)*处出现***NullPointerException***

为了克服这个异常，我们必须在 city 属性中加入一个 null 检查，这就违背了使用 Optional 的初衷。相反，我们可以将 *numberOfOrders* 参数声明为一个*整数*，如果它在方法执行期间不存在，则执行空值检查来默认它的值。

请参见下面的片段:

```
public void findNewCustomers(String city, Integer numberOfOrders) {
 List<Customer> allCustomers = .... ;

 Integer numOfOrders = numberOfOrders !=0 ? numberOfOrders : 0;
 allCustomers.stream()
 .filter(customer -> customer.city.equals(city))
 .filter(customer -> customer.numOrders >=numOfOrders)
 .collect(Collectors.*toList*());
 }
```

## **结论:**

Java 8 中引入的可选类提供了一个容器来表示引用的可选值。因此，它可以用来更好地处理值的存在或不存在，而不是使用空引用。因此，可以保留空引用来指示程序流中的中断或异常。请记住，引入 Optional 类并不是为了避免 Java 中的 NullPointerException，使用 Optional 并不是执行空检查的花哨的糖衣方式。

这些例子基于 Java 8 版本的可选实现。

最近的 Java 版本中增加了更多的方法如*或()*、 *ifPresentOrElse()、*和 *stream()* 供大家探索…