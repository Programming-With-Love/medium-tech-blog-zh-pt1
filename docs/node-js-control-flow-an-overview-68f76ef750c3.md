# Node.js 控制流—概述

> 原文：<https://medium.com/capital-one-tech/node-js-control-flow-an-overview-68f76ef750c3?source=collection_archive---------0----------------------->

## 异步初学者指南

![](img/c1b19068574e0be8e5aadf5198cdf4c5.png)

我发现大多数关于 Node.js 如何处理异步控制流的解释对我来说很难理解，因为这些解释要么假设 1)对计算机科学有很深的了解，要么假设 2)读者只对 Node.js 感兴趣，而不是它所构建的基础部分。

这篇文章是我填补这一空白的尝试。

# 总体并发性

在现代编程环境中，并发随处可见:

*   一个网站可能必须同时处理许多用户。
*   一组需要协调事务的 API 可能分布在云计算环境中的许多计算机上。
*   开发人员编写代码时，IDE 可能会在后台编译代码。

正如 Golang 的创造者之一罗布·派克所说:

> *“并发是独立执行的事物(通常是功能)的组合。”*

不幸的是，良好的代码设计在一个并发的环境中是很难的— *时期*。在并发程序中，开发人员必须管理多个有重叠时间线的活动。即使有一组相对简单的组件，复杂的通信模式或糟糕的组合也会导致竞争情况。众所周知，竞争条件很难测试和调试，因为它们经常受到环境条件的影响，如网络流量、操作系统的状态，甚至硬件的使用。

# Node.js 和线程

如 [MDN 文档](https://developer.mozilla.org/en-US/)所述，JavaScript 试图通过提供单线程[事件循环](https://developer.mozilla.org/en-US/docs/Web/JavaScript/EventLoop)来简化并发代码的问题。该事件循环负责:

*   执行代码。
*   收集和处理事件。
*   执行排队的子任务。

特别是，一旦一个函数开始运行，它将在程序的任何其他部分开始运行之前运行完成——您知道没有其他代码会破坏数据。但是如果你想建立一个 web 服务器，例如，并发性仍然需要解决。

## **默认异步**

为了提供并发性，Node.js 中耗时的操作(特别是 I/O 事件和 I/O 回调)在默认情况下是异步的。为了实现这种异步行为，modern Node.js 利用事件循环作为中央调度程序，将请求路由到 C++并将结果返回给 JavaScript。

在 I/O 中采用这种方法的决定从一开始就包含在 Node.js 中。Ryan Dahl(node . js 的创建者)在 NGINX 的启发下，[当时相信，](https://mappingthejourney.com/single-post/2017/08/31/episode-8-interview-with-ryan-dahl-creator-of-nodejs/)，*“也许我们做 I/O 是错误的，也许如果我们以非阻塞的方式做每件事，我们将解决编程中的许多困难。比如，也许我们可以完全忘记线程，只使用流程抽象和序列化通信。”*

然而，Node.js 最终还是不能放弃线程。

要了解 Node.js 在哪里利用线程实现并发性，以及它在哪里依赖于其他方法，重要的是要了解 Node.js 架构中的下一层是 [*libuv*](https://libuv.org/) ，这是一个专门为 Node.js 构建的 C 库。libuv 处理以下操作:

*   由 epoll、kqueue、IOCP、事件端口支持的全功能事件环路
*   异步 TCP 和 UDP 套接字
*   异步 DNS 解析
*   异步文件和文件系统操作
*   文件系统事件
*   子进程
*   *线程池*
*   信号处理
*   *线程和同步原语*

这个列表中的一些项目允许 libuv(和 Node.js)不关心线程。例如，使用操作系统事件通知系统(如 epoll 和 IOCP)来处理 web 服务器操作。

另一方面，libuv(和 Node.js)中的某些操作是同步和线程化的。如[所述，不要阻塞事件循环](https://nodejs.org/en/docs/guides/dont-block-the-event-loop/):

> “Node.js 有两种类型的线程:一个事件循环和 k 个工作线程。事件循环负责 JavaScript 回调和非阻塞 I/O，工作线程执行与完成异步请求的 C++代码对应的任务，包括阻塞 I/O 和 CPU 密集型工作。这两种类型的线程一次只能处理一个活动。

Node.js 使用工作池来处理“昂贵”的任务。这包括操作系统不提供非阻塞版本的 I/O，以及 CPU 密集型任务。

这些是利用该工作池的 Node.js 模块 API:

1.  **I/O 密集型**

*   DNS: dns.lookup()，dns.lookupService()。
*   文件系统:除 fs 之外的所有文件系统 API。FSWatcher()和那些显式同步的使用 libuv 的线程池。

**2。CPU 密集型**

*   Crypto: crypto.pbkdf2()、crypto.scrypt()、crypto.randomBytes()、crypto.randomFill()、crypto.generateKeyPair()。
*   Zlib:除了那些显式同步的以外，所有 zlib APIs 都使用 libuv 的线程池。"

## 你想要线索，你已经得到了！

Node.js 还提供了一个单独的 [Worker Thread](https://nodejs.org/api/worker_threads.html) 模块，用于开发人员希望执行 CPU 密集型 JavaScript 操作的情况(例如，文件压缩——这不打算由 I/O 操作使用)。工作线程模块允许开发人员创建自己的自定义线程池，并允许线程通过共享内存进行通信。

底线是:

*   对于 Node.js 擅长的许多用例(比如创建 web 服务器)，开发人员真的不必考虑线程，可以专注于“进程抽象”
*   但是线程是为有限的用例而存在的；如果你需要的话。

# 从回调到异步/等待的旅程

Node.js 中从回调到 async/await 的旅程反映了它与 [V8](https://v8.dev/docs) 的紧密关系——谷歌的开源 JavaScript 和 WebAssembly 引擎，用 C++编写。随着 V8 增加功能，Chrome 浏览器和 Node.js 都将把这些变化集成到它们的代码库中。

## 回调——驯服异步复杂性的首次尝试

最初，V8(和 Node.js)使用延续传递样式(CPS)模式处理异步代码。这种模式最初出现在 20 世纪 70 年代中期的 Scheme 语言中:

> *“以传递延续方式编写的函数需要一个额外的参数:一个显式的“延续”，即一个参数的函数。当 CPS 函数计算出它的结果值后，它通过调用 continuation 函数将这个值作为参数来“返回”它。这意味着当调用一个 CPS 函数时，调用函数需要提供一个用子例程的“返回”值调用的过程。——*[*【延续-传承风格】*](https://en.wikipedia.org/wiki/Continuation-passing_style#:~:text=In%20functional%20programming%2C%20continuation%2Dpassing,the%20usual%20style%20of%20programming.&text=Reynolds%20gives%20a%20detailed%20account%20of%20the%20numerous%20discoveries%20of%20continuations.)*[维基百科](https://en.wikipedia.org/wiki/Continuation-passing_style#:~:text=In%20functional%20programming%2C%20continuation%2Dpassing,the%20usual%20style%20of%20programming.&text=Reynolds%20gives%20a%20detailed%20account%20of%20the%20numerous%20discoveries%20of%20continuations.) [(CC BY-SA 3.0)](https://creativecommons.org/licenses/by-sa/3.0/legalcode)*

*在 JavaScript/Node.js 中，我们称之为*回调模式*。虽然回调函数对于简单的程序来说已经足够好了，但是在现实生活中，大多数情况下使用回调函数容易出错。例如，假设您有一个需要检索远程数据的网页，然后根据检索到的数据，需要加载更多的数据和一组图像。在这种情况下，您可能会陷入“回调地狱”——具有难以理解的成功和失败路径的深度嵌套回调。这是经典的“末日金字塔”(如 Mozilla 指南中关于[使用承诺](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Using_promises)的说明所示):*

```
*doSomething(function(result) {
doSomethingElse(result, function(newResult) {
    doThirdThing(newResult, function(finalResult) {
      console.log(‘Got the final result: ‘ + finalResult);
    }, failureCallback);
  }, failureCallback);
}, failureCallback);*
```

## *承诺——进一步简化*

*那么，如何避开末日金字塔呢？*许下诺言。**

*承诺的想法已经存在了一段时间。大约在 CPS 出现在 Scheme 的同时，处理异步的替代方法以不同的名称出现在各种编程语言中:futures、defers 等..在 1988 年， [Barbara Liskov 和 Liuba Shrira(在为 DARPA 做研究时)创造了术语 *promises*](https://heather.miller.am/teaching/cs7680/pdfs/liskov1988.pdf) 来描述一种构造，这种构造*“支持高效的异步远程过程调用机制，供分布式程序的组件使用。承诺是未来存在的价值的占位符。它是在发出呼叫时创建的。该调用计算承诺的价值，与发出调用的程序并行运行。当它完成时，其结果被存储在承诺中，然后可以被调用者‘认领’。”**

*2014 年 1 月发布的 Chrome 版本 32 引入了对承诺的支持。Node.js 于 2015 年 2 月跟进，在 0.12 中提供了 promise 支持。*

*与 1988 年描述的愿景非常相似，JavaScript 承诺是允许您组合并行运行调用的异步任务的对象；并且将呼叫管道化在一起:*

```
*doSomething()
.then(result => doSomethingElse(result))
.then(newResult => doThirdThing(newResult))
.then(finalResult => {
    console.log(`Got the final result: ${finalResult}`);
})
.catch(failureCallback);
});*
```

*在高层次上，承诺有三种状态:*

```
*//A new Promise starts in a “Pending” state
new Promise(function (resolve, reject) {
    reject(new Error(‘Transition to a “Rejected State”’))
    resolve({ message: ‘Transition to a “Fulfilled State”’})
})*
```

*作为一名开发人员，除了在 Node.js 中链接承诺之外，您还可以使用多种方法来组合承诺，以便更轻松地管理异步任务组:*

*   ***Promise.all** —这种方法通常在有多个相互依赖才能成功完成的异步任务时使用。all 接受一个可迭代的承诺作为输入，返回一个承诺作为输出。当输入的所有承诺都已解决并且非承诺已返回时，或者如果输入 iterable 不包含承诺，则此返回的承诺将得到解决。它会在任何输入承诺拒绝或未承诺抛出错误时立即拒绝，并将拒绝第一个拒绝消息/错误。*
*   ***Promise.race** —该方法返回一个承诺，只要 iterable 中的一个承诺满足或拒绝，该承诺就会满足或拒绝，并返回该承诺的值或原因。*
*   ***Promise.allSettled** —这种方法通常用在你有多个不依赖于对方成功完成的异步任务时，或者你总是想知道每个承诺的结果时。Promise.allSettled()返回一个承诺，该承诺在所有给定承诺完成或被拒绝后解析，并带有一个对象数组，每个对象描述每个承诺的结果，例如:*

```
*// asynchronous processing
// we will talk about async/await syntax in a minute
async processor(message) {
    return await someOperation(message);
    });
}// initial call
// inside of Promise.allSettled, map all messages to the asynchronous processor call
const results = await Promise.allSettled(messages.map(message => processor(message)));// values returned to results
//[
// {“status”:”rejected”,”reason”:”my first promise call failed”},
// {“status”:”fulfilled”,”value”:”success”}
//]*
```

****注:*** *Promise.allSettled 是 12.9.0 版本(2019 年 8 月发布)才引入 Node.js 的。同样，虽然使用原生 Node.js 功能进行组合的能力可以最大限度地减少依赖关系树，但有时也会牺牲性能——例如，根据至少一组测试* *，蓝鸟* *库可以快四倍。**

*在链接承诺的能力和组合多个承诺链的能力之间，V8(和 Node.js)消除了 CPS/callback 风格编程中固有的复杂性，而没有牺牲任何功能。*

## *async/Await——使承诺看起来像同步代码并提高性能*

*2017 年 10 月，V8 引入了 *async/await* 作为更复杂的 promises 语法的替代方案——同时，Node 8 也开始支持相同的语法。*

*Async/await 以与同步代码相同的方式构造异步代码。也就是说，使用 async/await 函数的代码与幕后承诺的操作非常相似:*

> *“Await 表达式通过一个异步函数暂停进程，产生控制，然后仅当一个等待的基于承诺的异步操作完成或被拒绝时才恢复进程。承诺的解析值被视为 await 表达式的返回值。使用 async / await 可以在异步代码周围使用普通的 try / catch 块。— [*【异步函数— JavaScript】由 Mozilla 贡献者*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)*[(CC BY-SA 4.0)](https://creativecommons.org/licenses/by-sa/4.0/legalcode)*[*Mozilla 文档*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function)***

**例如:**

```
**async function foo() {
try {
    const result = await doSomething();
    const newResult = await doSomethingElse(result);
    const finalResult = await doThirdThing(newResult);
    console.log(`Got the final result: ${finalResult}`);
} catch(error) {
    failureCallback(error);
  }
}**
```

**在可能的情况下，使用 *async/await* 比原来的 promise 设置有许多优点:**

*   **async/await 代码肯定比 CPS/callback 风格，甚至是 promise 语法更具可读性。**
*   **异步/等待代码[优于手写承诺代码](https://v8.dev/blog/fast-async) — *，但仅限于顺序承诺代码* —至少从 2018 年 11 月开始。也就是说，差异相对较小——例如，当我对 Node.js v12.16.2 运行 [V8 Promise 性能测试](https://github.com/v8/promise-performance-tests)时，我得到了以下结果:**

```
**Time(doxbee-async-es2017-native): 19.9 ms. Time(doxbee-promises-es2015-native): 26.3 ms.**
```

**关于 promise 性能的当前状态的更多细节可以在这个由 [Kuzzle 团队](https://blog.kuzzle.io/bluebird-native-async_await-javascript-promises-performances-2020)撰写的精彩文章中找到。**

## **Async/Await 和 Promises —一句警告**

**即使有了 async/await，简化异步代码仍然是一个挑战。例如，core Node.js 的撰稿人詹姆斯·斯内尔提供了七条建议来避免违背诺言:**

*   **知道你的代码何时被执行**
*   **不要使用意想不到的承诺**
*   **避免混淆承诺和回电**
*   **不要在循环中创造承诺**
*   **要明白同步承诺是没有用的**
*   **忌长。then()链**
*   **避免过度思考**

# **生成器和迭代器——产生控制的另一种方式**

**如上所述，*“Await 表达式通过一个异步函数暂停进程，产生控制，然后只有当一个等待的基于承诺的异步操作被完成或拒绝时才恢复进程。”***

**在 V8 和 Node.js 中获得控制权的另一种方式是通过生成器。虽然在最近的 Node.js 中直接使用生成器相对较少，但如果您偶然发现它们，它们仍然值得理解。**

## **作为协程的生成器**

**为了理解生成器以及它们与控制流的关系，理解子例程和协程的概念是很重要的。**

**一个*子程序*是一个执行特定任务的集中代码块(一个函数)，打包成一个单元。每当需要执行特定任务时，这个单元就可以在程序中使用。当子程序被调用时，执行从开始处开始，一旦子程序退出，它就结束了；子例程的实例只返回一次，并且在调用之间不保持状态。例如，在 JavaScript 中，同步子例程可能如下所示:**

```
**function add(x, y) {
return x + y;
}**
```

**子例程是结构化编程的一个主要部分，这是我们许多人在早期作为程序员接受教育时学到的一个范例。**

*   ****一个协程**也是一个集中式代码块。然而，与子程序不同的是，通过允许暂停和恢复执行，为*协作多任务*概括子程序。进程自动*周期性地产生*控制，以使多个任务能够同时运行。协程可以通过调用其他协程来退出，这些协程稍后可能会返回到它们在原始代码例程中被调用的位置。**
*   ****生成器**是一个“半协同程序”——协同程序的一个子集。**

**生成器和协程都可以产生多次，暂停它们的执行并允许在多个点重新进入。生成器只是将控制权转移回生成器的调用者，并传递一个值。在 JavaScript 世界中，所有生成器也是迭代器:**

```
**// code example borrowed from Arfat Salman at
// [https://codeburst.io/understanding-generators-in-es6-javascript-with-examples-6728834016d5](https://codeburst.io/understanding-generators-in-es6-javascript-with-examples-6728834016d5)function * generatorFunction() { // Line 1
    console.log(‘This will be executed first.’);
    yield ‘Hello, ‘; // Line 2
    console.log(‘I will be printed after the pause’);
    yield ‘World!’;
}const generatorObject = generatorFunction(); // Line 3
console.log(generatorObject.next().value); // Line 4
console.log(generatorObject.next().value); // Line 5
console.log(generatorObject.next().value); // Line 6/*********************************************/
/* Output begins */
/*********************************************/// This will be executed first.
// Hello,
// I will be printed after the pause
// World!
// undefined/*********************************************/
/* Output ends */
/*********************************************/**
```

**StackOverflow 上一个关于 Python 的[线程说得好:](https://stackoverflow.com/questions/29864366/difference-between-function-and-generator)**

> ***“生成器与返回数组的函数非常相似，因为生成器有参数，可以被调用，并生成一系列值。但是，生成器一次生成一个值，而不是构建一个包含所有值的数组并一次返回所有值，这需要较少的内存并允许调用者立即开始处理前几个值。简而言之，生成器看起来像函数，但行为像迭代器。”——*[*“函数和生成器的区别？”*](https://stackoverflow.com/questions/29864366/difference-between-function-and-generator) *上栈溢出无编码器* [(CC BY-SA 4.0)](https://creativecommons.org/licenses/by-sa/4.0/legalcode)**

## **什么是迭代器？**

**如前所述，生成器总是迭代器。因此，迭代器通常采用代码引用的形式，在执行时，计算容器中的下一项并返回它。当迭代器到达容器的末尾时，它返回一个商定的值。**

**迭代器和可迭代对象实际上分散在整个 JavaScript 中。例如，在幕后*展开语法*利用迭代:**

```
**const someString = ‘hi’
console.log([…someString]); // [“h”,”i”]**
```

**也就是说，JavaScript 中可迭代值最常见的用法可能是 for of 循环。例如，由于数组是可迭代的，所以下面的代码会按预期工作:**

```
**for (const element of [10,12,17,19]) {
console.log(element);
}**
```

**以下容器都是内置的可迭代对象，因为它们的每个原型对象都实现了一个@@iterator 方法:**

*   **线**
*   **排列**
*   **地图**
*   **一组**

**同样，JavaScript 中的许多 API 都接受可重复项，包括:**

*   **地图**
*   **一组**
*   **承诺。所有**
*   **承诺。都解决了**
*   **承诺.比赛**
*   **数组. from**

## **发电机——为什么？**

**一般来说，自定义迭代器和生成器不是您需要实现的东西——您可以主要利用 JavaScript 的内置迭代器和 async/await 语法的组合。特别是，它回避了这样一个问题——“*为什么生成器曾经在语言中实现过？”***

**在 *async/await* 操作之前的日子里，在像 [co](https://github.com/tj/co) 这样的库的帮助下，生成器可以被用来给开发者一个更干净的方法来处理异步代码:**

```
**// code example borrowed from Arfat Salman at
// [https://codeburst.io/understanding-generators-in-es6-javascript-with-examples-6728834016d5](https://codeburst.io/understanding-generators-in-es6-javascript-with-examples-6728834016d5)/* original code written with Promises */
function fetchJson(url) {
    return fetch(url)
    .then(request => request.text())
    .then(text => {
      return JSON.parse(text);
    })
    .catch(error => {
      console.log(`ERROR: ${error.stack}`);
    });
}/* using the co library to polyfill async/await */
const fetchJson = co.wrap(function* (url) {
    try {
      let request = yield fetch(url);
      let text = yield request.text();
      return JSON.parse(text);
    }
    catch (error) {
      console.log(`ERROR: ${error.stack}`);
    }
});/* the same call using plain old async/await */
async function fetchJson(url) {
    try {
      let request = await fetch(url);
      let text = await request.text();
      return JSON.parse(text);
    }
    catch (error) {
      console.log(`ERROR: ${error.stack}`);
    }
}**
```

**同样，在 2018 年 11 月 V8 优化底层实现之前，写得好的生成器代码实际上可以胜过类似的 async/await 代码。例如，参见[2015 年](https://glebbahmutov.com/blog/performance-of-v8-generators-vs-promises/)的这个析因试验。**

**综上所述，async/await 是 2020 年 Node.js 中占主导地位的异步范例。**

## **最后一点——简单的东西**

**像大多数语言一样，Node.js/JavaScript 有一套标准的结构化控制流构造，其操作方式与其他语言非常相似。有关这些的更多信息，请查看 [MDN](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Control_flow_and_error_handling) :**

*   **if/else**
*   **为**
*   **在…期间**
*   **尝试/抓住**
*   **扔**

# **把一切都包起来**

**理解 Node.js 控制流对于构建大规模应用程序来说是至关重要的，这些应用程序可以可靠地交付可预测的商业价值。我希望这篇文章能帮助软件工程师更好地理解 Node.js 如何处理这个问题。**

***披露声明:2020 首创一号。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。***