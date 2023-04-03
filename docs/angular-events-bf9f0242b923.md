# 角度事件

> 原文：<https://medium.com/quick-code/angular-events-bf9f0242b923?source=collection_archive---------0----------------------->

## 角度指南

## 在 Angular 中，我们可以使用不同类型的事件对象。有什么区别，我们应该用什么来做什么。

**角度事件发射器**

角度事件发射器是从“@anguar/core”包导入的。它在指令和组件中用于发出自定义事件。

它使用“@Output”和“@Input”装饰器。

EventEmmiter have**emit()**—这种方法可以让你向所有关注 Observable 的人发送消息。

也许在某个地方你可以找到方法**下一个()**。这个方法做的和 **emit()** 一样。在 Angular **的上一版本中，next()** 已被弃用。

**主题**

主题是从“rxjs”包导入的。主体是一种特殊的可观察对象。主体是一个观察者和被观察者，是一个积极的倾听者。它以 **next()** 触发。事件发射器建立在主题之上。不要忘记在离开组件的情况下取消订阅，以避免内存泄漏。

可观察和主体有两种方法:

*   **subscribe()** —该方法允许您订阅将发送可观察的消息。
*   **next()** —此方法允许您向所有关注 Observable 的人发送消息。

**总结**

当您想向其他组件发送通知时，在组件中使用 EvintEmitter 更好。

如果您有服务，并且希望向其他服务或组件发送通知，那么最好使用 Subject。此外，主题是容易操纵。例如，只需应用几个运算符，就可以轻松添加滤波和去抖功能。

private _subject:主题<any>；
建造师(){
这个。_subject =新主题<any>()；
这个。_ subject . pipe(
de bounce time(1000)，distinctUntilChanged() )
。subscribe(value = > { /*用值*/ })做点什么；
this.observer =这个。_ subject</any>

}

*原载于 2019 年 3 月 29 日*[*tomorrowmeannever.wordpress.com*](https://tomorrowmeannever.wordpress.com/2019/03/29/angular-events/)*。*