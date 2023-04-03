# JUnit 测试中的测试异常

> 原文：<https://medium.com/globant/testing-exceptions-in-junit-tests-3cc11f5caea3?source=collection_archive---------0----------------------->

JUnit 提供了跟踪异常以及检查代码是否抛出预期异常的功能。

**Junit4** 提供了一种异常测试的方式，你可以使用

*   @test 批注的可选参数(应为)

在对代码执行异常测试时，您需要确保您在 **@test annotation** 的可选参数中提供的异常类是相同的，否则我们的 **JUnit** 测试将会失败，因为它与您期望的来自方法的异常不匹配。

示例:**@ Test(expected = nullpointerexception . class)**

通过使用“expected”参数，您可以指定我们的测试可能抛出的异常名称。在上面的例子中，您使用了“**NullPointerException”**，如果开发人员使用了不允许的参数，测试将会抛出这个异常。

以空值作为输入测试排序数组的示例:

```
public class SortArrayTest {
    @Test
    public void sortNullArray(){
        int [] numbers = null;
        Arrays.*sort*(numbers);
    }
}
```

如果将运行上述测试用例，它将失败，并出现异常“**Java . lang . nullpointerexception**”。

因此，如果我们想为测试“ **NullPointerException** ”编写一个测试用例，我们可以对上面的代码进行如下修改:

```
public class SortArrayTest {
    @Test(expected = NullPointerException.class)
    public void sortNullArray(){
        int [] numbers = null;
        Arrays.*sort*(numbers);
    }
}
```

所以现在我们的测试用例将会给我们带来成功，我们可以跟踪异常。

快乐编码:)