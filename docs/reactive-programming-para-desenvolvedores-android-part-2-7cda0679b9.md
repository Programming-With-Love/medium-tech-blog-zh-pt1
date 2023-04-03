# 适用于 Android 开发人员的 Reactive Programming — Part 2

> 原文：<https://medium.com/google-developer-experts/reactive-programming-para-desenvolvedores-android-part-2-7cda0679b9?source=collection_archive---------1----------------------->

在上一篇 文章中,我介绍了反应式编程,在那里我开始谈论最初使用的“函数式反应式编程”命名法,以及为什么它没有定义这种模式,因为它与另一种称为“函数式编程”的模式混淆。

在澄清了命名法之后,我进一步写了关于被动思考的必要性,以及一些关于如何实现这种方法的理论例子,与编程早期使用的命令式方法相比。

在第二篇文章中,我将谈论:

*   如何配置项目(Android 或纯 Java 使用 Gradle)使用 RxJava 库
*   创建一个 Observable 以发出数据流。
*   创建一个订阅者以接收数据流。

# **什么是**RxJava?(T5 )****

很简单,反应性扩展作为一种模式,可以使用不同的语言来实现,并且由于一些开发人员社区的努力和承诺,已经为不同的现有语言创建了不同版本的库,例如:RxJava(Java),RxJs(JavaScript),RxPHP(Php)等可以在[这里找到。](http://reactivex.io/languages.html)

> “RxJava 是 Reactive Extensions 实现的 Java 库,它允许我们使用 Observers 序列创建基于事件的异步程序” — [WEB //github.com/ReactiveX/RxJava](https://github.com/ReactiveX/RxJava)

有几个针对 Java 的 Reactive Extensions 实现,但 RXJava 是 Java 的“*官方*”库,并且在 Android 上被广泛使用(有一些特定于平台的变体,我将在后面提到),其创建和维护背后的优秀贡献者社区。

# 如何在 Android 项目中配置 RxJava?

将 Android 或 Java 项目设置为使用 RXJava 非常简单,因为您只需将下面的代码添加到要使用该库的模块的 build.gradle 文件中即可导入库。

```
dependencies {
    //outras depenedenciescompile **"io.reactivex:rxjava:**$rootProject.ext.rxJavaVersion**"** }
```

*PS:RxJava 的版本在项目的 build.gradle 文件中被定义为一个变量,因为组织和易于在所有添加的 m*或*模块中保持相同的库版本。(T15)*

RxJava 有两个版本,对于这篇文章和接下来的文章将基于文档[中给出的第二个版本。](https://github.com/ReactiveX/RxJava)

# 创建数据发送者

在上一篇文章中,我谈到了反应式编程模式中的两个主要元素,其中一个负责发出数据流,另一个负责在接收数据时保持倾听并做出反应。

在 Java RX 中,负责发出数据流的组件来自以下类中实现的不同方式:

**Flowable:** 发行**零**或**N**项目,并支持**backpressure**(这是一个有趣且难以理解的概念,所以我可能会在以后的帖子中讨论)

**可观察的**:发出**零或 N**项,或者在发生**错误**或**完成**数据流发送时发出信号。
Observable 与 Flowable 的不同之处在于它不支持回压。

**Single:** 只发出一个带有 1 个元素或一个**错误的数据流。(T20)**

**Completeable:** 不要发出任何元素,只表示发生错误或数据流发送完成。

**Maybe:**顾名思义,这个实现可能不会发出任何项目、单个项目或错误信号。

出于启动原因,为了让你在跑步之前学习如何小便,我将首先介绍如何使用**Observable 发送数据的示例。(T26)**

## **如何使用 Observable 输出数据流。(T28)**

Observable 类公开了不同的静态方法来创建 observables 和组合不同的函数(我将在下一篇文章中介绍)。
创建观察值的一些最常用的方法是:

**just —** 创建一个 observable,它将传递的项目作为参数发出,并且当这个 observable 被创建时,这些项目开始发出,无论是否订阅了 Observer。

```
List<String> names = **new** ArrayList<>();
names.add(**"Dario"**);
*//Add more names to the list* Observable.*just*(names);
```

**defer** 创建一个 observable,只有在订阅了 Observer 之后,才会开始将传递的项目作为参数发出。正如下面的代码所示, creator **defer** 实际上接收一个函数作为参数,这是我们创建 Observable 时要创建的函数,通过使用方法 **just 返回另一个带有项目的 Observable。(T37)**

```
**final** List<String> names = **new** ArrayList<>();
names.add(**"Dario"**);
*//Add more names to the list* Observable.*defer*(**new** Func0<Observable<List<String>>>() {
    @Override
    **public** Observable<List<String>> call() {
        **return** Observable.*just*(names);
    }
}); 
```

**订阅 Observable 发送的数据流**

创建将接收数据流的 Observables 之后,为了完成整个电路,我们必须创建一个 Observer 来监听并响应要发送的数据流。

在 RxJava 中,观察者是由类***Subscriber***表示的。
subscriber 类实现了 Observer 接口,该接口具有 3 种重要的回调方法:

**onNext(T item)** — 每当从 Observable 接收项目时,此方法在 Subscriber 中调用。

**onComplete() —** 在 Observable 到达数据流的末尾时调用此方法。
当 observable 到达流的末尾,或者甚至没有发送任何项目时,可以调用此方法,并且 observable 仅用于发出异步操作已完成的信号。

onError(Throwable e) — T11 每当 Observable 遇到导致数据流中断的错误时,就会调用此方法。

*(例如,如果您正在对 API 进行 HTTP 调用,并且服务器使用已经支持 RxJava 的 Retrofit 之类的库返回错误,错误将被自动截获,而不是*是*在最简单的情况下,您需要使用 NetworkManager 之类的组件进行检查)*

## 加入 Observable 和 Subscriber

了解了这两个主要组成部分,现在是时候将所有组成部分放在一起,并展示这两个组成部分如何从头到尾相互关联。
在我看来,连接这两个组件的步骤是:

**1 — 拥有一个数据源( *Producer*** ):正如我在上面的 Observable 创建方法中所展示和提到的那样,**just** 和**defer** 都接收要发出的元素。
考虑到这一观察,我希望你内化:

*   Observable 不负责生成要发出的数据,但仅限于发出转换流并通知 Observer 订阅它。
*   数据源可以是对象、原始类型、返回两者之一的方法。

```
**public** List<String> getNamesList(){
    List<String> names = **new** ArrayList<>();
    names.add(**"Dario"**);
    names.add(**"Mario"**);
    names.add(**"Paulo"**);
    **return** names;
}
```

**2 — 使用我上面提到的方法创建一个观察表(T29)。(在这个例子中,我将使用 creator **just** )**

```
**public** Observable<List<String>> getNames(){
    **return** Observable.*just*(getNamesList());
}
```

3 — **创建一个订阅者**,该订阅者与 Observable 发出的数据类型相同。

```
Subscriber<List<String>> subscriber = **new** Subscriber<List<String>>(){
    @Override
    **public void** onCompleted() {
        *//a operacao terminou* }

    @Override
    **public void** onError(Throwable e) {
        *//ocorreu um erro e os detalhes estao no Throwable* }

    @Override
    **public void** onNext(List<String> names) {
        *//Fazer alguma coisa com a lista* }
};
```

**4 — 设置 Subscriber** 以监听 Observable 发送的流,并对三种方法之一(onNext、onError 或 onComplete)做出响应。

```
getNames().subscribe(subscriber);
```

在第四步中,当订阅订阅者到 Observable 时,如本示例中所示,不会发生任何错误,将调用包含名称列表的 onNext 方法,然后调用 onComplete 方法。

完整的示例见[GIST](https://gist.github.com/realdm/3c2f1f59bc3544ec87699ace8423f5f7) 。

在这篇文章中,我将停留在这里,在下一篇文章中,我将讨论我们可以应用于数据流的不同函数,以及为什么这些函数是本主题中最有趣的部分(在我看来),并使开发 android 应用程序时的工作变得更加容易。

*如果您发现这篇文章很有趣,请点击下面的绿色小心脏留下一个喜欢,并与您认为您也会喜欢的朋友分享:)*

吃下一个=)

DM