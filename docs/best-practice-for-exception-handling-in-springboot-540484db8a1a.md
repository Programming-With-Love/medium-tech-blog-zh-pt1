# SpringBoot 中异常处理的最佳实践

> 原文：<https://medium.com/globant/best-practice-for-exception-handling-in-springboot-540484db8a1a?source=collection_archive---------0----------------------->

每当我们想到在代码的任何级别处理异常时，我们都会在代码中到处写 try catch 块，然后过了几天，当我们试图检查我们的代码时，我们发现大部分代码都充满了处理异常。这降低了我们代码的可读性，也复制了许多我们可以很容易避免的日志消息。在这里，我们将尝试学习 Spring Boot 提供的强大功能，以避免这些重复，并提高代码的可读性，同时处理我们的应用程序中的异常。

# 概观

众所周知，异常处理是 SpringBoot Rest APIs 中最重要和最关键的事情，它帮助我们对代码执行有条件和无条件的检查，并以适当的方式处理任何类型的异常。除了它的好处之外，它也使代码变得复杂，使得未知用户不容易阅读代码。

因此，我们需要有一个公共场所，在那里我们可以管理和处理各种异常，并根据异常类型为 API 响应发送相应的错误代码。

在这篇博客中，我们将尝试了解一种简单的方法，这种方法可以使我们的代码在处理 SpringBoot 提供的异常时具有更好的格式。

# 描述

SpringBoot 在包 org . spring framework . web . bind . annotation 下提供了一个名为@ControllerAdvice 的非常强大的注释，这个注释让我们可以在应用程序的中心位置轻松处理各种异常。我们不需要分别在每个方法或类中捕获任何异常，相反，您可以从方法中抛出异常，然后它将在由@ControllerAdvice 注释的中央异常处理程序类中被捕获。任何用@ControllerAdvice 注释的类都将成为负责处理异常的 controller-advice 类。在这个类中，我们使用作为@ExceptionHandler、@ModelAttribute、@InitBinder 提供的注释。

@ControllerAdvice 是@Component 注释的专门化，它允许在一个全局处理组件中处理整个应用程序的异常。它可以被看作是由用@RequestMapping 和类似方法注释的方法抛出的异常的拦截器。

ResponseEntityExceptionHandler 是@ControllerAdvice 类的一个方便的基类，这些类希望通过@ExceptionHandler 方法提供跨所有@RequestMapping 方法的集中异常处理。它提供了处理内部 Spring MVC 异常的方法。它返回 ResponseEntity，而 DefaultHandlerExceptionResolver 返回 ModelAndView。

用@ExceptionHandler 注释的异常处理方法将捕获由声明的类抛出的异常，每当遇到相关的类型异常时，我们可以执行各种操作。我们可以捕捉各种异常，并根据需要处理的异常抛出各种 http 状态代码。下面的例子说明了我们如何捕捉各种异常并相应地发送相应的 http 状态代码。

@ exception handler(value = { datanotfoundexception . class })

public response entity<responsedto>> dataNotFoundException(InValidDataException ex){</responsedto>

response dtoresponse = new response dto(http status。BAD_REQUEST，常量 STATUS_FAIL，ex.getLocalizedMessage()，false)；

logger . error(" Data not found exception:"，ex)；

返回新的 ResponseEntity <responsedto>>(response，HttpStatus。BAD _ REQUEST)；</responsedto>

}

@ exception handler(value = { network exception . class })

公共响应实体<responsedto>网络异常(Exception ex) {</responsedto>

response dtoresponse = new response dto(http status。内部服务器错误，常量。STATUS_FAIL，ex.getLocalizedMessage()，false)；

LOGGER.error("网络异常: "，ex)；

返回新的 ResponseEntity <responsedto>>(response，HttpStatus。内部 _ 服务器 _ 错误)；</responsedto>

}

@ControllerAdvice 构造函数带有一些特殊的参数，允许您只扫描应用程序的相关部分，并只处理那些由构造函数中提到的相应类引发的异常。默认情况下，它会扫描并处理应用程序中的所有类。下面是一些类型，我们可以用它们来限制特定的类来处理异常。

1)注释——用提到的注释进行注释的控制器将得到@ControllerAdvice 注释类的帮助，并且有资格作为这些类的例外

例如，@ControllerAdvice(annotations = rest controller . class)—这里由@ controller advice 注释的异常助手将捕获由@RestController 注释类抛出的所有异常。

2)基础包—通过指定我们要扫描的包并处理这些包的异常。

例如@ controller advice(base packages = " org . example . controllers ")—这将只扫描调用提到的包并处理相同的异常。

3) assignableTypes —该参数将确保扫描和处理来自所提到的类的异常

例如@ controller advice(assignable types = { controller interface . class，

AbstractController.class})

# 在使用@ControllerAdvice 之前

在下面的代码片段中，我们看到有许多重复的行，控制器代码也不容易阅读，因为每个 API 中有多个 try 和 catch 块。

[@ rest controller](http://twitter.com/RestController)
[@ request mapping](http://twitter.com/RequestMapping)(path = "/employees ")
公共类 EmployeeController {

private static final Logger Logger = Logger factory . get Logger(employee controller . class)；

私人雇员道；

[@ get mapping](http://twitter.com/GetMapping)(path = "/{ employeeId } "，produces = " application/JSON ")
public response entity<Employee>get employees([@ path variable](http://twitter.com/PathVariable)Long employeeId){
response entity<Employee>response = null；
try {
if(null = = employeeId | | position Id . equals(0L)){
throw new InvalidInputException("员工 Id 无效")；
}
employee = employee Dao . getemployeedetails(employeeId)；
响应=新响应实体<员工>(员工，HttpStatus。OK)；
}
catch(InvalidInputException e){
logger . error("无效输入:"，e . getmessage())；
响应=新响应实体<员工>(员工，HttpStatus。BAD _ REQUEST)；
}
catch(Business Exception e){
logger . error(" Business Exception:"，e . getmessage())；
响应=新响应实体<员工>(员工，HttpStatus。内部 _ 服务器 _ 错误)；
}
catch(异常 e) {
Logger.error("系统错误:"，e . getmessage())；
response = new response entity<员工>(员工，HttpStatus。内部 _ 服务器 _ 错误)；
}
返回响应；
}

}

**使用@ControllerAdvice 后**

下面的代码片段使代码易于阅读，也减少了重复的行。

[@ rest controller](http://twitter.com/RestController)
[@ request mapping](http://twitter.com/RequestMapping)(path = "/employees ")
公共类 EmployeeController {

private static final Logger Logger = Logger factory . get Logger(employee controller . class)；

[@ get mapping](http://twitter.com/GetMapping)(path = "/{ employeeId } "，produces = " application/JSON ")
public response entity<Employee>get employees([@ path variable](http://twitter.com/PathVariable)Long employeeId){
if(null = = employeeId | | position Id . equals(0L)){
抛出新的 InvalidInputException("员工 Id 无效")；
}
Employee Employee = Employee Dao . getemployeedetails(employeeId)；
返回新的 ResponseEntity <员工>(员工，HttpStatus。OK)；；
}

}

# 结论

通过阅读上面的例子，我们将能够发现使用@ControllerAdvice 将解决我们在维护代码质量方面的许多问题，并且它将增加代码的可读性。这将有助于每个人在应用程序的公共位置调试所有错误。使用这种方法，我们还可以很容易地针对任何类型的错误更新记录器，并保持错误消息的一致性。