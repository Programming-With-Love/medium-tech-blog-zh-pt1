# 科特林—入门第 2 部分

> 原文：<https://medium.easyread.co/kotlin-getting-started-part-2-285099c77b32?source=collection_archive---------2----------------------->

![](img/7ee6f6d744cc61f21ddd94e2a6be77f2.png)

## 操作游戏攻略！

你可能想看看我以前的文章[这里](https://medium.com/@mochadwi/kotlin-getting-started-4e1520c7536f)

# 运营和优先事项

## 转换数据类型(转换)

```
var n1Str:String = “12”var n2:Int = n1Str.toInt() // cast it to int
```

## 数学运算

常见的 [PEMDAS](https://www.khanacademy.org/math/pre-algebra/pre-algebra-arith-prop/pre-algebra-order-of-operations/v/more-complicated-order-of-operations-example)

```
var n1 = 1var n2 = 2var sum = n1 + n2 * 4 / 2 // PEMDAS, priorities
```

## 增量和减量

```
var sum = ++n1 + n2 // n1 will have the increment by 1 first
```

## 格式化

```
// let's print the above result to the console using formatting
print(“sum: $sum”)
```

好了，今天到此为止！

***快乐，编码的家伙们！！！***