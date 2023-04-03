# 通过做坏事做好事:用 Go 编写糟糕的代码第 1 部分

> 原文：<https://medium.com/capital-one-tech/doing-well-by-doing-bad-writing-bad-code-with-go-part-1-2dbb96ce079a?source=collection_archive---------0----------------------->

## 对围棋编程的讽刺

![](img/eb9945204bc3f4c30f467aba5dea73f7.png)

经过几十年的 Java 编程，在过去的几年里，我主要从事围棋方面的工作。现在，在 Go 中工作很棒，主要是因为代码很容易理解。Java 通过去除多重继承、手动内存管理和操作符重载，简化了 C++编程模型。Go 做了同样的事情，并通过完全去除继承和函数重载，继续向简单、直接的编程风格发展。简单明了的代码是可读的代码，可读的代码是可维护的代码。这对我的公司和同事来说太好了。

像所有的文化一样，软件工程也有它自己的传奇，在饮水机旁窃窃私语的故事。我们都听说过关于开发人员的谣言，他们不是专注于交付最好的产品，而是专注于工作保障。他们不想要可维护的代码，因为这意味着其他人可以理解他们在做什么。围棋能做到这一点吗？他们如何让围棋代码尽可能难懂？不容易。让我们探索一些选择。

我肯定你在想，*“你能把一门编程语言滥用到什么地步？有没有可能在围棋中写出如此恐怖的代码，以至于有人可能变得不可替代？”不要担心。当我还是大学生的时候，我有一个项目，维护一个研究生写的 Lisp 代码。他设法用 Lisp 语言编写了 Fortran 语言。代码看起来像这样:*

```
(defun add-mult-pi (in1 in2)
    (setq a in1)
    (setq b in2)
    (setq c (+ a b))
    (setq d (* 3.1415 c)
    d
)
```

有很多这样的代码文件。这是绝对的可怕和绝对的天才。我花了几个月的时间试图理解这些代码。与此相比，编写糟糕的 Go 代码是小菜一碟。

有很多方法可以让代码变得糟糕，但是我们只关注其中的几种。为了正确地作恶，你必须知道如何行善。我们将一次一个地讨论这些要点，看看道貌岸然的 go 程序员是如何做事的，然后看看我们如何能做完全相反的事情。

## **包装差**

包是一个很好的起点，因为它们是如此谦逊。如何组织你的代码？

在 Go 中，包的名字用来指代导出的物品；我们写 *`fmt.Println`* 或者 *`http.RegisterFunc`* 。因为包名非常明显，优秀的 Go 开发人员会确保包名描述了导出的项目。我们不应该把包命名为 util，因为我们不想要像 *`util.JSONMarshal`* 这样的名字，我们想要像 *`json.Marshal`* 这样的名字。

优秀的 Go 开发人员也不会创建单一的 DAO 或模型包。对于那些不熟悉这个术语的人来说，DAO 是一个*“数据访问对象”* —与数据库对话的代码层。我曾经在一个地方工作，六个 Java 服务都用相同的 Dao 导入相同的库来访问它们共享的相同数据库，因为，你知道，微服务？

如果您有一个包含所有 Dao 的包，那么很可能会导致包之间的循环依赖，而这是 Go 所不允许的。如果您有多个服务将单个 DAO 包作为一个库，那么您也可能会遇到这样的情况:为了支持一个服务而进行的更改需要您升级所有的服务，否则就会出现问题。这就是所谓的分布式整体，一旦你建立了一个，就很难进行更新。

一旦你知道包装应该如何工作，以及它能防止什么，就很容易变得邪恶。糟糕地组织你的代码，给你的包起一个糟糕的名字。把你的代码分成像 model、util 和 dao 这样的包。如果你真的想测试边界，看看你是否能以你的宠物或你最喜欢的颜色命名包装。当人们因为试图以一种稍微新的方式使用你的代码而以循环依赖或分布式独石告终时，你会感叹并告诉他们他们做错了。

## **接口不当**

现在我们已经把包都弄乱了，我们可以继续讨论接口了。Go 中的接口不像其他语言中的接口。你没有显式地声明一个类型实现一个接口，这个事实起初看起来是一个小细节，但是它实际上完全颠覆了接口的概念。

在大多数具有抽象类型的语言中，接口是在实现之前或同时定义的。您必须这样做，因为迟早您将需要在一个接口的不同实现中进行交换，即使只是为了测试。如果您没有提前创建接口，您就不能在不破坏所有使用该类的代码的情况下返回并插入接口，因为它们将不得不被重写以引用接口而不是具体类型。

出于这个原因，Java 代码通常具有用许多方法编写的巨大服务接口。依赖于这些接口的类使用它们需要的方法，忽略其他的。编写测试是可能的，但是你已经增加了一个额外的抽象层，当编写测试时，你经常会退回到使用工具来生成那些你不关心的方法的实现。

在 Go 中，隐式接口定义了你需要使用什么方法。拥有接口的是使用代码，而不是提供代码。即使您正在使用一个定义了大量方法的类型，您也可以指定一个只包含您需要的方法的接口。使用同一类型不同部分的不同代码将定义不同的接口，这些接口只覆盖它们需要的功能。通常，这些接口只有几个方法。

这使您的代码更容易理解，因为您的方法或函数声明不仅定义了它需要什么数据，还确切地定义了它将使用什么功能。这也是优秀的 Go 开发者遵循建议的一个原因，*“接受接口，返回结构。”*

但是，你知道，仅仅因为这是一个好的实践并不意味着你*有*去做。

让你的接口变坏的最好方法是回到其他语言的做法，提前定义接口，作为被调用代码的一部分。定义真正大的接口，有许多方法，由服务的所有客户端共享。这使得人们不清楚实际上需要哪些方法，这就造成了复杂性，而复杂性是邪恶的程序员的朋友。

## **人口传递指针**

在我谈论这意味着什么之前，我们需要一点哲学。如果你退后一步想一想，每一个程序都做着完全相同的事情。每个程序接收数据，处理数据，然后将处理后的数据发送到某个地方。无论您是在编写一个工资系统，接受 HTTP 请求并返回网页，还是检查操纵杆以查看按下了什么按钮，从而知道在屏幕上显示什么样的移动，都是如此。程序处理数据。

如果你这样看待程序，你能做的最重要的事情就是确保它易于理解数据是如何被转换的。这就是为什么在数据流经代码时尽可能保持数据不变是个好主意。因为不变的数据是容易跟踪的数据。

在 Go 中，我们有引用类型和值类型。两者的区别在于变量是引用数据还是引用数据在内存中的位置。指针、切片、映射、通道、接口、函数都是引用类型，其他的都是值类型。如果将一个值类型的变量赋给另一个变量，它会复制该值；对一个变量的改变不会改变另一个变量的值。

将一个引用类型变量赋给另一个引用类型变量意味着它们共享同一个内存位置，所以如果你改变一个引用类型变量所指向的数据，你也改变了另一个引用类型变量所指向的数据。对于局部变量和函数的参数都是如此。

```
func main() {
    //value types
    a := 1
    b := a
    b = 2
    fmt.Println(a, b) // prints 1 2 //reference types
    c := &a
    *c = 3
    fmt.Println(a, b, *c) // prints 3 2 3
}
```

优秀的 Go 开发者希望让数据收集变得容易理解。他们确保尽可能多地使用函数的值参数。Go 无法将 struct final 中的字段或函数的参数标记为 final，但如果函数有值参数，修改参数的值不会改变调用函数中变量的值。被调用函数所能做的就是向调用函数返回值。考虑到这一点，如果您通过调用接受值参数的函数来填充结构，那么您可以将值组合到结构中，并且可以清楚地知道结构中每个值的确切来源。

```
type Foo struct {
    A int
    B string
}func getA() int {
    return 20
}func getB(i int) string {
    return fmt.Sprintf("%d",i*2)
}func main() {
    f := Foo{}
    f.A = getA()
    f.B = getB(f.A)
    //I know exactly what went into building f
    fmt.Println(f)
}
```

那么我们如何变得邪恶呢？我们通过反转这个模型来实现。

不是调用返回我们一起合成的值的函数，而是将一个指向结构的指针传递给函数，让它们对结构进行更改。因为每个函数都有完整的结构，所以知道哪些字段被修改的唯一方法是查看所有代码。函数之间也可以有不可见的依赖关系，一个函数放入第二个函数需要的数据，但是代码中没有任何内容表明必须首先调用第一个函数。如果你以这种方式构建你的数据结构，你肯定没有人会理解你的代码在做什么。

```
type Foo struct {
    A int
    B string
}func setA(f *Foo) {
    f.A = 20
}//Secret dependency on f.A hanging out here!
func setB(f *Foo) {
    f.B = fmt.Sprintf("%d", f.A*2)
}func main() {
    f := Foo{}
    setA(&f)
    setB(&f)
    //Who knows what setA and setB
    //are doing or depending on?
    fmt.Println(f)
}
```

## **传播恐慌**

现在我们开始错误处理。也许你认为拥有大约 75%错误处理的程序是非常邪恶的，我不会说你完全错了。Go 代码有很多错误处理，前端和中心。当然，如果有一种方法能让它不那么刺眼就好了。但是错误是会发生的，你如何处理错误是专业人士和业余人士的区别。糟糕的错误处理会产生难以调试和维护的不稳定程序。有时做好意味着做艰苦的工作。

```
func (dus DBUserService) Load(id int) (User, error) {
    rows, err := dus.DB.Query("SELECT name FROM USERS WHERE ID = ?", id)
    if err != nil {
        return User{}, err
    }
    if !rows.Next() {
        return User{}, fmt.Errorf("no user for id %d", id)
    }
    var name string
    err = rows.Scan(&name)
    if err != nil {
        return User{}, err
    }
    err = rows.Close()
    if err != nil {
        return User{}, err
    }
    return User{Id: id, Name: name}, nil
}
```

许多语言，如 C++、Python、Ruby 和 Java，都使用异常来处理错误。如果出错，这些语言的开发人员会抛出或引发一个异常，期望某个地方的一些代码会处理它。当然，这取决于代码的客户端是否知道甚至有可能抛出异常，因为，对于 Java 的检查异常中的异常(没有双关语的意思)，在有异常的语言中，函数或方法签名中没有任何东西告诉您异常可能会发生。那么开发人员如何知道需要担心哪些异常呢？他们有两个选择:

*   首先，他们可以通读代码调用的所有库的所有源代码，以及这些库调用的所有库，等等。
*   其次，他们可以信任文档。也许我已经厌倦了，但是我个人觉得很难相信这些文档。

那么，我们如何让这个邪恶的品牌消失呢？滥用恐慌和恢复关键词。恐慌是指类似于*“磁盘消失”*或*“网卡爆炸”的情况*它不适合像*这样的东西，“有人传递了一个字符串而不是一个 int。”*但也有可能。

不幸的是，其他不太开明的开发人员会从他们的代码中返回错误，所以这里有一个小助手函数 PanicIfErr。利用这一点把其他开发人员的错误变成恐慌。

```
func PanicIfErr(err error) {
    if err != nil {
        panic(err)
    }
}
```

您可以使用 PanicIfErr 来包装其他人的错误，压缩您的代码，不再有丑陋的错误处理。任何你可能犯的错误现在都是恐慌。太有生产力了！

```
func (dus DBUserService) LoadEvil(id int) User {
    rows, err := dus.DB.Query(
                 "SELECT name FROM USERS WHERE ID = ?", id)
    PanicIfErr(err)
    if !rows.Next() {
        panic(fmt.Sprintf("no user for id %d", id))
    }
    var name string
    PanicIfErr(rows.Scan(&name))
    PanicIfErr(rows.Close())
    return User{Id: id, Name: name}
}
```

你可以在顶部附近的某个地方放一个恢复，也许是在你自己的中间件中，并且说你不仅处理了错误，你还使每个人的代码更干净。假装做好事来做坏事是最好的邪恶。

```
func PanicMiddleware(h http.Handler) http.Handler {
    return http.HandlerFunc(
        func(rw http.ResponseWriter, req *http.Request){
            defer func() {
                if r := recover(); r != nil {
                   fmt.Println("Yeah, something happened.")
                }
            }()
            h.ServeHTTP(rw, req)
        }
    )
}
```

## **副作用设置**

接下来，我们将根据副作用进行配置。记住，一个优秀的 Go 开发者想要理解数据是如何通过他们的程序流动的。最好的方法是通过在应用程序中显式地配置依赖关系来了解数据流经的内容。即使符合相同接口的事物也可能有非常不同的行为来符合该接口，就像将数据存储在内存中的代码与调用数据库来完成相同工作的代码之间的差异。然而，有一些方法可以在不进行显式调用的情况下在 Go 中设置依赖关系。

就像许多其他语言一样，Go 有一种不直接调用代码就能神奇地运行代码的方法。如果创建一个名为 init 的不带参数的函数，该函数会在加载其包时自动运行。更令人困惑的是，如果在一个文件中有多个名为 init 的函数，或者在一个包的多个文件中有多个名为 init 的函数，那么它们都将运行。

```
package accounttype Account struct{
    Id int
    UserId int
}func init() {
    fmt.Println("I run magically!")
}func init() {
    fmt.Println("I also run magically, and I am also named init()")
}
```

Init 函数通常与空白导入成对出现。Go 有一种特殊的导入声明，看起来像*` import _ " github . com/lib/pq `*。当您将空白标识符作为导入包的名称时，会在包中运行 init 方法，但不会公开包的任何标识符。对于一些 Go 库——如数据库驱动程序和图像格式——您必须通过在应用程序中的某个地方包含包的空白导入来加载它们，只是为了触发包中的 init 函数，以便它可以注册一些代码。

```
package mainimport _ "github.com/lib/pq"func main() {
    db, err := sql.Open(
        "postgres",
        "postgres://jon@localhost/evil?sslmode=disable")
}
```

现在，这显然是个坏主意。当您使用 init 函数时，您的代码会神奇地运行，完全不受开发人员的控制。Go 最佳实践不鼓励使用 init 函数。它们是一个晦涩的特性，它们混淆了代码流，并且很容易隐藏在库中。

换句话说，init 函数非常适合我们邪恶的目的。您可以使用 init 函数和空白导入来设置应用程序的状态，而不是显式配置或注册包中的项目。在这个例子中，我们通过注册表使 account 对应用程序的其余部分可用，account 包使用 init 函数将自己放入注册表中。

```
package accountimport (
    "fmt"
    "github.com/evil-go/example/registry"
)type StubAccountService struct {}func (a StubAccountService) GetBalance(accountId int) int {
    return 1000000
}func init() {
    registry.Register("account", StubAccountService{})
}
```

如果你想使用一个帐号，你可以在你的程序中放一个空白的导入。不一定要是 main，不一定是相关代码，只要是**某处** 即可。太神奇了！

```
package mainimport (
    _ "github.com/evil-go/example/account"
   "github.com/evil-go/example/registry"
)type Balancer interface {
    GetBalance(int) int
}func main() {
    a := registry.Get("account").(Balancer)
    money := a.GetBalance(12345)
}
```

如果您在库中使用 inits 来设置依赖项，您会看到其他开发人员绞尽脑汁，想知道这些依赖项是如何设置的，以及如何更改它们。除了你没有人会知道。

## **复杂的配置**

我们可以通过配置做更多的事情。如果你是一名优秀的 Go 开发人员，你应该将配置与程序的其他部分隔离开来。在 main()函数中，您从环境中捕获属性，并将它们转换为明确连接在一起的组件所需的值。您的组件不知道任何关于属性文件或这些属性如何命名的信息。对于简单的组件，您可以设置公共属性，在更复杂的情况下，您可以创建一个工厂函数，该函数接收配置信息并返回正确配置的组件。

```
func main() {
    b, err := ioutil.ReadFile("account.json")
    if err != nil {
    fmt.Errorf("error reading config file: %v", err)
    os.Exit(1)
    }
    m := map[string]interface{}{}
    json.Unmarshal(b, &m)
    prefix := m["account.prefix"].(string)
    maker := account.NewMaker(prefix)
}type Maker struct {
    prefix string
}func (m Maker) NewAccount(name string) Account {
    return Account{Name: name, Id: m.prefix + "-12345"}
}func NewMaker(prefix string) Maker {
    return Maker{prefix: prefix}
}
```

但是邪恶的开发人员知道最好在整个程序中散布配置信息。不要在您的包中有一个函数来定义您的包需要的值的名称和类型，而是使用一个函数，该函数接受一个字符串映射来自己进行字符串和转换。

如果这看起来太不友好，可以使用 init 函数从包内部加载一个属性文件，并自己设置值。看起来您让其他开发人员的生活变得更加轻松，但是您知道得更多。

使用 init 函数，您可以在代码深处定义新的属性，在它们进入生产环境之前，没有人会发现它们，并且所有东西都会崩溃，因为正确启动所需的十几个不同的属性文件中有一个丢失了某些东西。如果你想要额外的邪恶力量，你可以建立一个 wiki 来跟踪所有图书馆的所有属性，并定期“忘记”加入新的属性。作为财产的保管人，你成为唯一能让软件运行的人。

```
func (m maker) NewAccount(name string) Account {
    return Account{Name: name, Id: m.prefix + "-12345"}
}var Maker maker
func init() {
    b, _ := ioutil.ReadFile("account.json")
    m := map[string]interface{}{}
    json.Unmarshal(b, &m)
    Maker.prefix = m["account.prefix"].(string)
}
```

## **功能框架**

最后，我们来看框架与库。差别是微妙的。这不仅仅是尺寸的问题。你可以有大的库和小的框架。框架调用你的代码，而你调用库的代码。框架要求你以特定的方式编写代码，无论是命名你的方法，还是确保它们符合特定的接口，或者让你用框架注册你的代码。框架将它们的需求倾倒在你的代码中。一般来说，框架拥有你。

Go 鼓励库，因为库是可组合的。当然，每个库都希望数据以某种格式传入，您可以编写一点粘合代码，将一个库的输出加入到另一个库的输入中。

对于框架，很难让它们很好地合作，因为每个框架都希望完全控制在它内部运行的代码的生命周期。通常，让框架一起工作的唯一方法是让框架的作者聚在一起，明确地相互支持。利用框架的邪恶来获得持久的力量的最好方法是:编写一个只在内部使用的定制框架。

## **曾经和未来的罪恶**

一旦你掌握了这些技巧，你就会走上邪恶之路。在我的下一篇博文中，我将向您展示一种部署所有这些邪恶的方法，以及当您将好代码转换为邪恶代码时会是什么样子。

## [**此处阅读第二部分。**](/capital-one-tech/doing-well-by-doing-bad-writing-bad-code-with-go-part-2-e270d305c9f7)

*声明:这些观点仅代表作者个人观点。除非本帖中另有说明，否则 Capital One 不属于所提及的任何公司，也不被其认可。使用或展示的所有商标和其他知识产权都是其各自所有者的所有权。本文为 2018 首都一。*