# 使用类方法在 python 中生成通用对象

> 原文：<https://medium.com/quick-code/support-your-class-users-by-providing-an-alternate-way-of-creating-class-instances-using-class-e2da42241913?source=collection_archive---------0----------------------->

## 当 __init__ 方法不支持您希望类用户能够创建类实例的所有方式时

![](img/26d2839ef12a108747cd50730c5aecf5.png)

Photo by E[v](https://unsplash.com/@5tep5?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)erton Vila on [Unsplash](https://unsplash.com/s/photos/traffic-light?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

类方法是很好的替代构造函数，可以用作工厂方法，通过支持不同的实例化参数来创建类的实例。

## 让我们来看一个例子

假设您有一个非常简单的日期类，如下所示，可以通过提供三个参数进行实例化:年、月和日

用户将如下实例化该类

```
date = Date(2020, 04, 05)
print(date)
```

这很好

```
> Date(2020, 04, 05)
```

但是，假设您想为您的类的用户提供创建日期实例的替代方法。例如，用户应该能够从“YYYY-MM-DD”格式的单个字符串参数创建日期实例。也许像下面这样

```
date = Date(‘2020–04–05’)
```

## **在不破坏默认的 __init__ 方法的情况下，如何在 Date 类中支持这个实例化方法？**

解决的办法是使用一个**的*类方法！***

python 中的类方法必须使用***@ class method***decorator 进行修饰

我们所做的是创建一个名为 ***from_str*** 的新方法，该方法使用单个字符串参数接受新的期望的实例化方式。第一个参数总是类对象本身，在本例中是 Date。这个方法中没有任何魔法，因为它只是将字符串参数分解成一个年、月和日的元组，然后使用该结果依次调用 __init__ 方法

现在我们可以使用这个类方法来实例化

```
date = Date.from_str(‘2020–04–05’)
print(date)
```

我们得到了同样的结果

```
> Date(2020, 04, 05)
```

## 资源

> * [Github](https://gist.github.com/i4ali/c92b0e1018edbe5e54c82f590871c350)