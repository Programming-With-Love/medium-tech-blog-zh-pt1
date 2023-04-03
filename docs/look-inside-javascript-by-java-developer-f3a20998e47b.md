# Java 开发人员对 JavaScript 的深入了解

> 原文：<https://medium.com/capital-one-tech/look-inside-javascript-by-java-developer-f3a20998e47b?source=collection_archive---------2----------------------->

## 我已经做了九个月的网页开发员了！

![](img/ccf62c3119e1e8f47d0530f7821c78c2.png)

在此之前，我做了五年的 Android/iOS 开发人员，开发过 Java 和 Swift 这两种基于编译器的语言。但是在过去的九个月里，我一直在用基于运行时语言的 JavaScript 编写代码。

这些语言有一些特征差异。下面是我转行到 web 开发时发现的最有趣的一个小列表。

# **1。静态打字与动态打字**

Java 是一种静态类型语言，它将代码编译成 JVM 字节码，然后解释成机器语言。相反，JavaScript 是一种动态类型语言，它在运行时解释代码。这在实践中意味着什么？

用两种语言思考这个例子:

## **Java**

```
public float getShipping(String state) {
    return ON_CAMPAIGN ? 0.f : SHIPPING.get(state);
}public float getTotalPrice(String price, String state) {
    float withShipping = price + getShipping(state); // Compiler error
     ...
}
```

## java 描述语言

```
function shipping(state) {
    return onCampaign ? 0 : shipping[state];
}function totalPrice(price, state) {
    ...
    const withShipping = price + shipping(state);      
    console.log(withShipping);              // this prints ???
}
```

由于类型不匹配，上面的 Java 代码无法编译。Java 告诉你，你正在做的事情是错误的，以防止在你运行代码之前出现小错误。

上面的 JavaScript 代码不会让您知道可能有问题。虽然乍一看可能没什么问题，但是让我们想想这段代码打印出了什么。

当 ***价格*** 为 10.0 且 ***发货【状态】*** 返回 2.0，则 console.log 输出 12.0。如果 ***发货【状态】*** 返回“2.0”怎么办？现在 ***console.log*** 打印出“102.0”。在运行之前，我们无法保证这段代码会产生什么结果。

使用基于编译器的语言，如 Java，您需要预先付出努力来修复可能的错误，并减少运行时出错的机会。这样，通过编译器就像一种单元测试，你需要通过它来验证你的逻辑。而对于基于运行时的语言，比如 JavaScript，直到运行时你才会意识到错误。为了防止它们，你需要写更多的测试来确保你将得到更少的运行时错误。

# **2。猴子补丁 vs 无猴子补丁**

JavaScript 的一个特性是你可以动态地修改函数的行为；这种技术被称为猴子补丁。也就是说，*不用对应用程序代码做任何修改，就可以交换一个依赖的实现。*

在 Java 中，由于代码必须被编译，所以在运行代码之前，每个类和函数的定义都必须被定义(不可变)。依赖注入必须到位，以便能够在运行代码之前交换行为。

让我们看一个例子。假设我有一个名为 order.js 的类，我使用 Jest 测试了 order.js 中的一个函数。

## **order.js**

```
const shipping = require('./shipping');
const tax = require('./tax');module.exports = {
    totalPrice: function totalPrice (price, state) {
        const shippingFee = shipping.fee(state);
        const tax = tax.get(price, state);

        return price + shippingFee + tax;
    }
}
```

您可以看到 ***shipping*** 和 ***tax*** 是本地依赖项，在该文件中需要使用。这是测试。

## **order-test.js**

```
jest.mock('./tax');
jest.mock('./order');const shipping = require('./shipping');
const tax = require('./tax');
const order = require('./order');it('should return total price with shipping and tax', function () {
    shipping.fee = jest.fn();
    tax.get = jest.fn();

    shipping.fee.mockReturnValue(6.0);
    tax.get.mockReturnValue(7.5); expect(order.totalPrice(100.0, 'CA')).toEqual(113.5);
});
```

在这个测试文件中，我用 Jest 函数重写了 ***shipping.fee*** 和 ***tax.get*** 的实现，而 order.js 中没有任何特殊代码。当***order . total price***在那一行之后运行时， ***shipping.fee*** 和 ***tax.get*** 不是 order.js 相反，它成为 Jest 的实现。这部分归功于 Jest 框架；然而底线是 JavaScript 可以动态地覆盖函数的行为。

在 Java 中，同样的测试不像在 JavaScript 中那么瘦。Java 是一种基于编译器的语言，所以所有的功能都与我定义的内容绑定在一起，并且在运行时不可互换。因此，如果我为一个具有本地依赖的类编写测试，我必须要么通过公共函数让这个类接受依赖，要么使用依赖注入框架来处理依赖。

为了演示这一点，我将使用名为 [Dagger](https://google.github.io/dagger/) 的 Java 依赖注入框架重写上面的 JavaScript 测试示例。

我将使用 [JUnit](http://junit.org/junit4/) 进行 Java 测试。测试场景与上面的 JavaScript 示例相同。我有一个叫 Order.java 的类，我在 Order.java 测试一个函数。

首先，Order 类需要以依赖注入的方式编写。

## **Order.java**

```
public class Order {

    @Inject
    Shipping shipping;

    @Inject
    Tax tax;

    public Order() {
        DaggerOrderComponent.*builder*()
            .orderModule(new OrderModule())
            .build()
            .inject(this);
    }

    public float getTotalPrice(float price, String state) {
        float shippingFee = shipping.fee(state);
        float taxValue = tax.get(price, state);

        return price + shippingFee + taxValue;
    }
}
```

@Inject 是 Dagger 注释，使一个字段可以从这个类和***daggerordercomponent . builder()外部注入。orderModule(新的 OrderModule())。构建()。注射(这个)；*** 将注入所有@Inject 注释字段(依赖)。

其次，我需要准备一个类来注入生产代码依赖，另一个类来注入测试代码依赖(即被模仿的对象)到订单类。

## **OrderModule.java(用于生产代码相关性)**

```
@Module
public class OrderModule {

    @Provides
    @Singleton
    public Shipping provideShipping() {
        return new Shipping();
    }

    @Provides
    @Singleton
    public Tax provideTax() {
        return new Tax();
    }
}
```

@Module 类包含一组函数，这些函数提供了要注入的依赖项。这些函数被注释为@Provides，它返回一个类，该类将被注入到另一个类的@Inject 注释字段中。为了测试，我不想使用实际的*和 ***税*** 类。相反，我想用这些的模拟类。因此，我为测试编写了@Module 类，如下所示:*

*我将使用 [Mockito](http://site.mockito.org/) 来生成模拟类。*

## ***TestOrderModule.java(用于测试代码依赖)***

```
*@Module
public class OrderTestModule {

    @Mock
    Shipping shipping;

    @Mock
    Tax tax;

    public OrderTestModule() {
        MockitoAnnotations.initMocks(this);
    }

    @Provides
    @Singleton
    public Shipping provideShipping() {
        return shipping;
    }

    @Provides
    @Singleton
    public Tax provideTax() {
        return tax;
    }
}*
```

*现在，我可以将这个测试模块注入到 Order 类中，这样我就可以通过使用模拟的类来测试 Order 类。*

## ***OrderTest.java***

```
*@Test
public void testGetTotalPrice() {
    OrderTestModule module = new OrderTestModule();
    Order order = new Order(); // Inject mocked classes in Order class        
    DaggerOrderTestComponent
        .*builder*()
        .orderTestModule(module)
        .build()
        .inject(order);

    *when*(module.shipping.fee("")).thenReturn(6.f);
    *when*(module.tax.get(0.f, "")).thenReturn(7.5f); float result = order.getTotalPrice(100.f, "CA");

    *assertEquals*(result, 113.5f);
}*
```

*正如您所注意到的，与 JavaScript 相比，具有本地依赖性的 Java 需要更多的代码来使类可测试。基于编译器的语言，如 Java，由于其语言模型，不能动态地修改函数的实现，而 JavaScript 可以动态地改变函数的行为。*

# *3.面向对象编程与函数式编程*

*包括 Java 在内的面向对象编程(OOP)有一个对象的概念，其中一个类定义对象的属性(字段)并控制这些属性(方法)。类别会维护物件的状态。*

*包括 JavaScript 在内的函数式编程是通过计算数据来定义的，不需要保持状态，也不需要用函数来改变状态。这种范式中的函数只依赖于它的输入，而输出是由只基于输入的计算产生的。*

*让我们看一个真实的例子。假设我下面有一个 cart.js 文件。这个函数不包含任何状态，每个函数只依赖于它的输入。*

## ***cart.js***

```
*module.exports = { addItem: function (cart, newItem, qty) {
    const itemsInCart = cart.items.filter(function (item, index) {
      return item.id === newItem.id;
    }); // closeDeep from lodash
    const updatedCart = cloneDeep(cart);
    if (itemsInCart.length === 0) {
      updatedCart.items.push({
        item: newItem,
        qty
      });
    } // Get total price of cart contents.
    // (omit implementation of totalPrice)
    updatedCart.totalPrice = totalPrice(updatedCart.items); return updatedCart;
  }, updateQty: function (cart, itemId, newQty) {
    const updatedItems = cart.items.map(function (item) {
      if (item.id === itemId) {
        item.qty = newQty;
      }
      return item;
    });

    const updatedCart = cloneDeep(cart);
    updatedCart.items = updatedItems;
    // (omit implementation of totalPrice)
    updatedCart.totalPrice = totalPrice(updatedItems); return updatedCart;
  }
}*
```

*由于函数式编程不保留任何状态，所以“cart”需要总是被传递。*

*如果我利用 OOP 的一个原则——*封装*——用 OOP 范式把它翻译成 Java，我可以写如下。*

## ***Cart.java***

```
*public class Cart {

    private ProductDao productDao;

    private float totalPrice = 0.f;
    private List<Product> items = new ArrayList<>(); public Cart() {
        productDao = new ProductDao();
    } public void addItem(String productId, String qty) {
        if (indexOfItem(productId) == -1) {
            tems.add(productDao.get(productId));
        }
        // (Omit the implementation)
        updateTotalPrice();
    }

    public void updateQty(String productId, int qty) {
        Product product = productDao.get(productId);
        product.setQty(qty);
        items.set(indexOfItem(productId), product);
        // (Omit the implementation)
        updateTotalPrice();
    } public float getTotalPrice() {
        return totalPrice;
    }

    public List<Product> getItems() {
        return items;
    }

    private int indexOfItem(String productId) {
        for (int i = 0; i < items.size(); i++) {
            if (items.get(i).getProductId().equals(productId)) {
                return i;
            }
        }

        return -1;
    }
}*
```

*这个 Cart 类跟踪属性 ***总价*** 和 ***项目*** 。因此，类的使用者只传递最少的信息来执行操作。然而，在 JavaScript 示例中，传递给该函数的 ***cart*** 可能不会在每次调用该函数时都具有相同的结构。在 Java 示例中，用户只能执行某些操作，不能直接接触属性。*

*函数式编程要求所有函数都是纯函数。因此，它易于测试、易于调试，并且是线程安全的(完全没有状态！).然而，由于所有函数都依赖于输入，因此消费者有责任跟踪从函数返回并作为输入传递给其他函数的数据。*

*面向对象编程保留了状态，并封装了对状态的控制，减少了一些重复代码，增加了代码的可重用性。一般来说，消费者应该能够在不知道函数实现的情况下使用函数，因为复杂的逻辑/状态管理隐藏在包含函数的对象内部。*

# ***结论***

*从移动开发过渡到 web 开发是一个有趣的旅程！JavaScript 和 Java 是两种截然不同的语言，直到我做出改变，我才意识到这一点。我已经列出了一些我觉得有趣的主要区别，但是你还可以列出更多。*

*两者都有特定的优势，了解各自的特点将有助于您确定哪种语言最适合哪种情况。当然，这不仅仅适用于 JavaScript 和 Java。您可以将此应用于任何静态类型或动态类型语言，以确定哪种语言适合您的项目。这同样适用于面向对象编程或函数式编程语言。关键的一点是，无论你选择哪一种，都要了解它的特点，并确保利用它的特长！*

**声明:这些观点仅代表作者个人观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权都是其各自所有者的所有权。本文为 2017 首都一。**

**欲了解更多关于 Capital One 的 API、开源、社区活动和开发者文化，请访问我们的一站式开发者门户 DevExchange。*[*https://developer.capitalone.com/*](https://developer.capitalone.com/)*