# GO:方法接收器指针 v/s 值

> 原文：<https://medium.com/globant/go-method-receiver-pointer-vs-value-ffc5ab7acdb?source=collection_archive---------0----------------------->

![](img/f2869fe772af8be4a522bafd3330ac3c.png)

如果您是新手，那么您一定已经接触过方法和函数的概念。让我们找出两者的区别-

**通过指定参数、返回值和函数体的类型来声明函数**。

```
type Person struct {
    Name string
    Age  int
}func NewPerson(name string, age int) *Person {
  return &Person{
     Name: name,
     Age:  age,
  }
}
```

**方法**只是一个带有接收方参数的函数。它是用相同的语法声明的，只是增加了**接收器**。

```
func (p *Person) isAdult bool {
  return p.Age > 18
}
```

在上面的方法声明中，我们在`*Person`类型上声明了`isAdult`方法。

现在我们将看到**值接收器**和**指针接收器*之间的区别。***

**值接收器**复制一份类型，并将其传递给函数。函数堆栈现在保存一个相等的对象，但是在内存的不同位置。这意味着对传递的对象所做的任何更改都将保留在该方法的本地。原始对象将保持不变。

**指针接收器**将类型的地址传递给函数。函数堆栈有一个对原始对象的引用。所以对传递的对象的任何修改都会修改原始对象。

让我们用例子来理解这一点-

```
package mainimport (
  "fmt"
)type Person struct {
    Name string
    Age  int
}func ValueReceiver(p Person) {
    p.Name = "John"
    fmt.Println("Inside ValueReceiver : ", p.Name)
}func PointerReceiver(p *Person) {
    p.Age = 24
    fmt.Println("Inside PointerReceiver model: ", p.Age)
}func main() {
    p := Person{"Tom", 28}
    p1:= &Person{"Patric", 68} ValueReceiver(p)
fmt.Println("Inside Main after value receiver : ", p.Name)
    PointerReceiver(p1)
fmt.Println("Inside Main after value receiver : ", p1.Age)
}O/P-
Inside ValueReceiver :  John
Inside Main after value receiver :  Tom
Inside PointerReceiver :  24
Inside Main after pointer receiver :  24
```

这表明带有值接收者的方法修改了对象的副本，而原始对象保持不变。通过 ValueReceiver 方法将一个人的 Like- Name 从 Tom 更改为 John，但是这种更改没有反映在 main 方法中。另一方面，带有指针接收器的方法修改实际的对象。通过指针接收器方法，人的年龄从 68 岁变为 24 岁，同样的变化也反映在主方法中。您可以通过打印出指针或值接收器操作前后的对象地址来检查事实。

**那么如何在指针与值接收器之间选择呢？**

如果你想在一个方法中改变接收器的状态，操纵它的值，**使用指针接收器**。这对于按值复制的值接收者来说是不可能的。对值接收者的任何修改都是对该副本的本地修改。如果不需要操纵接收器值，**使用一个值接收器**。

指针接收器避免在每次方法调用时复制值。如果接收器是一个大的结构，

值接收器是并发安全的，而指针接收器不是并发安全的。因此，程序员需要照顾它。

**注意事项**—

1.  尽可能对所有方法使用相同的接收器类型。
2.  如果需要状态修改，使用指针接收器，如果不使用值接收器。

 [## 围棋之旅

### 编辑描述

tour.golang.org](https://tour.golang.org/methods/8)