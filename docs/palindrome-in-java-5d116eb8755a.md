# 如何检查一个数字或字符串？

> 原文：<https://medium.com/edureka/palindrome-in-java-5d116eb8755a?source=collection_archive---------1----------------------->

![](img/d864dab63947374f68cb993fd64b8d38.png)

当人们面试 Java 时，通常会测试他们的逻辑和编程技能。最常见的问题之一是 Java 中的回文程序。一个回文只不过是一个数字或者一个字符串，当它被反转时保持不变。比如 ***12321*** 或者 ***MAAM*** 。很明显，字母在反转时形成镜像。

我介绍了以下几个方面，它们展示了在 Java 中检查回文的多种方法:

*   使用 While 循环的回文程序
*   使用 For 循环的回文程序
*   使用库方法的回文程序(字符串)

# 使用 While 循环的回文程序

这是使用“For Loop”找到回文程序的最简单的程序之一。让我们深入一个例子来检查给定的输入是否是回文。

```
public class PalindromeProgram { public static void main(String[] args)
 { 
     int rem, rev= 0, temp; 
   int n=121; // user defined number to be checked for palindrome      temp = n;       // reversed integer is stored in variable 
      while( n != 0 )
      {
         rem= n % 10; 
         rev= rev * 10 + rem;
         n=n/10; 
     }
 // palindrome if orignalInteger(temp) and reversedInteger(rev) are equal     if (temp == rev)
         System.out.println(temp + " is a palindrome.");
    else 
         System.out.println(temp + " is not a palindrome."); 
    }
}
```

**输出:** 121 是一个回文数

**说明**:输入你要检查的数字，存储在一个临时(temp)变量中。现在反转数字，比较临时数字是否与反转的数字相同。如果两个数相同，它将打印回文数，否则不是回文数。

**注:**回文程序的逻辑不变，只是执行不同。

现在你清楚了逻辑，让我们试着用另一种方式用 Java 实现回文程序，也就是使用 while 循环。

# 使用 For 循环的回文程序

```
public class PalindromeProgram {
   public static void main(String[] args)

 {
     int n=1234521, rev=0, rem, temp;      temp = n; for( ;n != 0; n /= 10 )
      {
         rem = n % 10; 
         rev= rev* 10 + rem;
       }     // palindrome if temp and sum are equal 
    if (temp== rev) 
        System.out.println(temp + " is a palindrome."); 
   else
         System.out.println(temp + " is not a palindrome."); 
     } 
}
```

**输出:** 1234521 不是回文

**解释:**在上面的程序中，数字不是回文。逻辑保持不变，只是使用了“for”循环而不是 while 循环。在每次迭代中，执行 num/= 10，并且 conditionnum！= 0 被选中。

# Java 中的回文程序(字符串)使用库方法

在这一节中，我们将找到一个 Java 字符串的回文。它的工作原理和整数一样，比如“madam”是回文，但“madame”不是回文。让我们用 Java 实现这个回文程序，使用一个字符串反转函数。

```
class PalindromeProgram 
{
public static void checkPalindrome(String s) 
{ 
// reverse the given
String String reverse = new StringBuffer(s).reverse().toString();// checks whether the string is palindrome or not
if (s.equals(reverse))
System.out.println("Yes, it is a palindrome"); else 
System.out.println("No, it is not a palindrome");
}
public static void main (String[] args) 
throws java.lang.Exception 
{ 
checkPalindrome("madam");
}
}
```

**输出:**对，是回文

**解释:**在上面的代码中，我们已经使用了一个字符串反转函数来计算一个数的倒数，然后将这个数与原数进行比较。如果两个数相同，它将打印回文数，否则不是回文数。

到此，我们结束了这个博客。如果你想查看更多关于人工智能、DevOps、道德黑客等市场最热门技术的文章，你可以参考 Edureka 的官方网站。

请留意本系列中的其他文章，它们将解释 Java 的各个方面。

> 1. [Java 教程](/edureka/java-tutorial-bbdd28a2acd7)
> 
> 2.[Java 中的继承](/edureka/inheritance-in-java-f638d3ed559e)
> 
> 3.[Java 中的多态性](/edureka/polymorphism-in-java-9559e3641b9b)
> 
> 4.[Java 中的抽象](/edureka/java-abstraction-d2d790c09037)
> 
> 5. [Java 字符串](/edureka/java-string-68e5d0ca331f)
> 
> 6. [Java 数组](/edureka/java-array-tutorial-50299ef85e5)
> 
> 7. [Java 集合](/edureka/java-collections-6d50b013aef8)
> 
> 8. [Java 线程](/edureka/java-thread-bfb08e4eb691)
> 
> 9.[Java servlet 简介](/edureka/java-servlets-62f583d69c7e)
> 
> 10. [Servlet 和 JSP 教程](/edureka/servlet-and-jsp-tutorial-ef2e2ab9ee2a)
> 
> 11.[Java 中的异常处理](/edureka/java-exception-handling-7bd07435508c)
> 
> 12.[高级 Java 教程](/edureka/advanced-java-tutorial-f6ebac5175ec)
> 
> 13. [Java 面试问题](/edureka/java-interview-questions-1d59b9c53973)
> 
> 14. [Java 程序](/edureka/java-programs-1e3220df2e76)
> 
> 15.[科特林 vs Java](/edureka/kotlin-vs-java-4f8653f38c04)
> 
> 16.[依赖注入使用 Spring Boot](/edureka/what-is-dependency-injection-5006b53af782)
> 
> 17.[Java 中的可比](/edureka/comparable-in-java-e9cfa7be7ff7)
> 
> 18.[十大 Java 框架](/edureka/java-frameworks-5d52f3211f39)
> 
> 19. [Java 反射 API](/edureka/java-reflection-api-d38f3f5513fc)
> 
> 20.[Java 中的 30 大模式](/edureka/pattern-programs-in-java-f33186c711c8)
> 
> 21.[核心 Java 备忘单](/edureka/java-cheat-sheet-3ad4d174012c)
> 
> 22.[Java 中的套接字编程](/edureka/socket-programming-in-java-f09b82facd0)
> 
> 23. [Java OOP 备忘单](/edureka/java-oop-cheat-sheet-9c6ebb5e1175)
> 
> 24.[Java 中的注释](/edureka/annotations-in-java-9847d531d2bb)
> 
> 25.[Java 中的图书管理系统项目](/edureka/library-management-system-project-in-java-b003acba7f17)
> 
> 26.[Java 中的树](/edureka/java-binary-tree-caede8dfada5)
> 
> 27.[Java 中的机器学习](/edureka/machine-learning-in-java-db872998f368)
> 
> 28.[Java 中的顶级数据结构&算法](/edureka/data-structures-algorithms-in-java-d27e915db1c5)
> 
> 29. [Java 开发者技能](/edureka/java-developer-skills-83983e3d3b92)
> 
> 30.[前 55 个 Servlet 面试问题](/edureka/servlet-interview-questions-266b8fbb4b2d)
> 
> 31. [](/edureka/java-exception-handling-7bd07435508c) [顶级 Java 项目](/edureka/java-projects-db51097281e3)
> 
> 32. [Java 字符串备忘单](/edureka/java-string-cheat-sheet-9a91a6b46540)
> 
> 33.[Java 中的嵌套类](/edureka/nested-classes-java-f1987805e7e3)
> 
> 34. [Java 集合面试问答](/edureka/java-collections-interview-questions-162c5d7ef078)
> 
> 35.[Java 中如何处理死锁？](/edureka/deadlock-in-java-5d1e4f0338d5)
> 
> 36.[你需要知道的 50 大 Java 集合面试问题](/edureka/java-collections-interview-questions-6d20f552773e)
> 
> 37.[Java 中的字符串池是什么概念？](/edureka/java-string-pool-5b5b3b327bdf)
> 
> 38.[C、C++和 Java 有什么区别？](/edureka/difference-between-c-cpp-and-java-625c4e91fb95)
> 
> 39.[Java 中的回文——如何检查一个数字或字符串？](/edureka/palindrome-in-java-5d116eb8755a)
> 
> 40.[你需要知道的顶级 MVC 面试问答](/edureka/mvc-interview-questions-cd568f6d7c2e)
> 
> 41.[Java 编程语言的十大应用](/edureka/applications-of-java-11e64f9588b0)
> 
> 42.[Java 中的死锁](/edureka/deadlock-in-java-5d1e4f0338d5)
> 
> 43.[Java 中的平方和平方根](/edureka/java-sqrt-method-59354a700571)
> 
> 44.[Java 中的类型转换](/edureka/type-casting-in-java-ac4cd7e0bbe1)
> 
> 45.[Java 中的运算符及其类型](/edureka/operators-in-java-fd05a7445c0a)
> 
> 46.[Java 中的析构函数](/edureka/destructor-in-java-21cc46ed48fc)
> 
> 47.[爪哇的二分搜索法](/edureka/binary-search-in-java-cf40e927a8d3)
> 
> 48.[Java 中的 MVC 架构](/edureka/mvc-architecture-in-java-a85952ae2684)
> 
> 49. [Hibernate 面试问答](/edureka/hibernate-interview-questions-78b45ec5cce8)

*原载于 2019 年 6 月 11 日*[*【https://www.edureka.co】*](https://www.edureka.co/blog/palindrome-in-java)*。*