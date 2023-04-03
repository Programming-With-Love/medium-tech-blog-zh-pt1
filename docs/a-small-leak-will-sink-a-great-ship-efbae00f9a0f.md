# 一个小漏洞会使一艘大船沉没

> 原文：<https://medium.com/square-corner-blog/a-small-leak-will-sink-a-great-ship-efbae00f9a0f?source=collection_archive---------0----------------------->

## 在 Android Lollipop 之前，警告对话框可能会导致 Android 应用程序中的内存泄漏。

*作者写的*[](https://twitter.com/Piwai)**。**

> *注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)*

**当我在构建 LeakCanary 的时候，这篇文章是作为一个内部邮件线程开始的。我发现了一个奇怪的内存泄漏，并开始挖掘，以找出发生了什么。**

*TL；DR:在 Android Lollipop 之前，警告对话框可能会导致你的 Android 应用程序内存泄漏。*

# *艺术家*

*我收到来自 [LeakCanary](http://squ.re/leakcanary) 的内存泄漏报告:*

```
** GC ROOT thread com.squareup.picasso.Dispatcher.DispatcherThread.<Java Local>
* references android.os.Message.obj
* references com.example.MyActivity$MyDialogClickListener.this$0
* leaks com.example.MyActivity.MainActivity instance*
```

*简单地说:Picasso 线程将消息实例作为堆栈上的局部变量。该消息引用了一个对话接口。OnClickListener，它本身引用了一个销毁的活动。*

*局部变量通常是短命的，因为它们只存在于堆栈中。在线程上调用方法时，会分配一个堆栈帧。当该方法返回时，堆栈帧被清除，其所有局部变量被垃圾回收。如果一个局部变量导致了泄漏，那么这通常意味着一个线程正在循环或阻塞，并在这样做的同时保持对一个消息实例的引用。*

*Dimitris 和我看了毕加索的源代码。*

*调度员。DispatcherThread 是一个简单的处理程序 Thread:*

```
***static** **class** **DispatcherThread** **extends** HandlerThread **{**
  DispatcherThread**()** **{**
    **super(**Utils**.**THREAD_PREFIX **+** DISPATCHER_THREAD_NAME**,** THREAD_PRIORITY_BACKGROUND**);**
  **}**
**}***
```

*该线程通过以非常标准的方式实现的处理程序接收消息:*

```
***private** **static** **class** **DispatcherHandler** **extends** Handler **{**
  **private** **final** Dispatcher dispatcher**;** **public** **DispatcherHandler(**Looper looper**,** Dispatcher dispatcher**)** **{**
    **super(**looper**);**
    **this.**dispatcher **=** dispatcher**;**
  **}** @Override **public** **void** **handleMessage(final** Message msg**)** **{**
    **switch** **(**msg**.**what**)** **{**
      **case** REQUEST_SUBMIT: **{**
        Action action **=** **(**Action**)** msg**.**obj**;**
        dispatcher**.**performSubmit**(**action**);**
        **break;**
      **}**
      *// ... handles other types of messages*
    **}**
  **}**
**}***
```

*这是一个死胡同。Dispatcher 中没有明显的 bug。dispatcher handler . handle Message()，它通过局部变量以某种方式保存对消息的引用。*

# *队列提示*

*最终，出现了更多的内存泄漏报告。不仅仅是毕加索。我们会从各种类型的线程中得到局部变量泄漏，并且总会涉及到一个对话框点击监听器。泄漏线程有一个共同的特征:它们是工作线程，通过某种阻塞队列接收任务。*

*让我们看看 HandlerThread 是如何工作的:*

```
***for** **(;;)** **{**
    Message msg **=** queue**.**next**();** *// might block*
    **if** **(**msg **==** **null)** **{**
        **return;**
    **}**
    msg**.**target**.**dispatchMessage**(**msg**);**
    msg**.**recycleUnchecked**();**
**}***
```

*肯定有一个引用消息的局部变量。然而，它应该是非常短暂的，一旦循环迭代就被清除。*

*我们试图通过编写一个带有阻塞队列的基本工作线程并只向它发送一条消息来进行复制:*

```
***static** **class** **MyMessage** **{**
  **final** String message**;**
  MyMessage**(**String message**)** **{**
    **this.**message **=** message**;**
  **}**
**}****static** **void** **startThread()** **{**
  **final** BlockingQueue**<**MyMessage**>** queue **=** **new** LinkedBlockingQueue**<>();**
  MyMessage message **=** **new** **MyMessage(**"Hello Leaking World"**);**
  queue**.**offer**(**message**);**
  **new** **Thread()** **{**
    @Override **public** **void** **run()** **{**
      **try** **{**
        loop**(**queue**);**
      **}** **catch** **(**InterruptedException e**)** **{**
        **throw** **new** **RuntimeException(**e**);**
      **}**
    **}**
  **}.**start**();**
**}****static** **void** **loop(**BlockingQueue**<**MyMessage**>** queue**)** **throws** InterruptedException **{**
  **while** **(true)** **{**
    MyMessage message **=** queue**.**take**();**
    System**.**out**.**println**(**"Received: " **+** message**);**
  **}**
**}***
```

*一旦消息被打印到日志中，我们期望 MyMessage 实例被垃圾收集。*

*LeakCanary 不同意:*

```
** GC ROOT thread com.example.MyActivity$2.<Java Local> (named 'Thread-110')
* leaks com.example.MyActivity$MyMessage instance*
```

*当我们向队列发送新消息时，之前的消息被垃圾收集，新消息现在正在泄漏。*

*在 VM 中，每个堆栈帧都有一组局部变量。垃圾收集器是保守的:如果有一个引用可能是活的，它不会收集它。*

*迭代之后，局部变量不再可达，但是它仍然保存对消息的引用。一旦引用不再可达，解释器/JIT 就可以手动将它置空，但它只是保持引用活动，并假设不会造成任何损害。*

*为了证实这一理论，我们手动将引用设置为 null 并再次打印，这样 null 就不会被优化掉:*

```
***static** **void** **loop(**BlockingQueue**<**MyMessage**>** queue**)** **throws** InterruptedException **{**
  **while** **(true)** **{**
    MyMessage message **=** queue**.**take**();**
    System**.**out**.**println**(**"Received: " **+** message**);**
    message **=** **null;**
    System**.**out**.**println**(**"Now null: " **+** message**);**
  **}**
**}***
```

*在测试上述更改时，我们看到在 Message 设置为 null 后，MyMessage 实例立即被垃圾收集。我们关于 VM 忽略本地消息变量的理论似乎得到了证实。*

*由于这种泄漏可能在各种线程和队列实现中重现，我们现在可以肯定这是一个 VM 错误。最重要的是，我们只能在 Dalvik 虚拟机上复制它，而不是在 ART 虚拟机或 JVM 上。*

# *(回收)瓶中的信息*

*我们发现了一个 bug，但是它会造成巨大的内存泄漏吗？让我们再来看看我们最初的泄漏:*

```
** GC ROOT thread com.squareup.picasso.Dispatcher.DispatcherThread.<Java Local>
* references android.os.Message.obj
* references com.example.MyActivity$MyDialogClickListener.this$0
* leaks com.example.MyActivity.MainActivity instance*
```

*在我们发送给 Picasso dispatcher 线程的消息中，我们从未将 Message.objto 设置为 dialog interface . onclick listener。它怎么会出现在那里呢？*

*此外，在消息被处理后，它立即被回收，Message.obj 被设置为 null。只有这样，HandlerThread 才会等待下一条消息，并暂时泄漏前一条消息:*

```
***for** **(;;)** **{**
    Message msg **=** queue**.**next**();** *// might block*
    **if** **(**msg **==** **null)** **{**
        **return;**
    **}**
    msg**.**target**.**dispatchMessage**(**msg**);**
    msg**.**recycleUnchecked**();**
**}***
```

*就这一点而言，我们知道泄漏的消息已被回收，因此不会保留其先前的内容。*

*一旦回收，消息将返回到静态池中:*

```
***void** **recycleUnchecked()** **{**
    *// Mark the message as in use while it remains in the recycled object pool.*
    *// Clear out all other details.*
    flags **=** FLAG_IN_USE**;**
    what **=** 0**;**
    arg1 **=** 0**;**
    arg2 **=** 0**;**
    obj **=** **null;**
    replyTo **=** **null;**
    sendingUid **=** **-**1**;**
    when **=** 0**;**
    target **=** **null;**
    callback **=** **null;**
    data **=** **null;** **synchronized** **(**sPoolSync**)** **{**
        **if** **(**sPoolSize **<** MAX_POOL_SIZE**)** **{**
            next **=** sPool**;**
            sPool **=** **this;**
            sPoolSize**++;**
        **}**
    **}**
**}***
```

*我们有一个泄漏的空消息，可能会被重用并填充不同的内容。消息总是以相同的方式使用:从池中分离，填充内容，放入 MessageQueue，然后处理，最后回收并放回池中。*

*因此，它不应该长时间保存其内容。为什么我们最后总是泄露 DialogInterface。OnClickListener 实例？*

# *警报对话框*

*让我们创建一个简单的警告对话框:*

```
***new** AlertDialog**.**Builder**(this)**
    **.**setPositiveButton**(**"Baguette"**,** **new** DialogInterface**.**OnClickListener**()** **{**
      @Override **public** **void** **onClick(**DialogInterface dialog**,** **int** which**)** **{**
        MyActivity**.**this**.**makeBread**();**
      **}**
    **})**
    **.**show**();***
```

*请注意，点击侦听器有一个对活动的引用。匿名类被翻译成以下代码:*

```
**// First anonymous class of MyActivity.*
**class** **MyActivity$0** **implements** DialogInterface**.**OnClickListener **{**
  **final** MyActivity **this**$0**;**
  MyActivity$0**(**MyActivity **this**$0**)** **{**
    **this.**this$0 **=** **this**$0**;**
  **}**
  @Override **public** **void** **onClick(**DialogInterface dialog**,** **int** which**)** **{**
    **this**$0**.**makeBread**();**
  **}**
**}****new** AlertDialog**.**Builder**(this)**
    **.**setPositiveButton**(**"Baguette"**,** **new** **MyActivity$0(this));**
    **.**show**();***
```

*在内部，AlertDialog 将工作委托给 AlertController:*

```
**/***
 ** Sets a click listener or a message to be sent when the button is clicked.*
 ** You only need to pass one of {@code listener} or {@code msg}.*
 **/*
**public** **void** **setButton(int** whichButton**,** CharSequence text**,**
        DialogInterface**.**OnClickListener listener**,** Message msg**)** **{**
    **if** **(**msg **==** **null** **&&** listener **!=** **null)** **{**
        msg **=** mHandler**.**obtainMessage**(**whichButton**,** listener**);**
    **}**
    **switch** **(**whichButton**)** **{**
        **case** DialogInterface**.**BUTTON_POSITIVE**:**
            mButtonPositiveText **=** text**;**
            mButtonPositiveMessage **=** msg**;**
            **break;**
        **case** DialogInterface**.**BUTTON_NEGATIVE**:**
            mButtonNegativeText **=** text**;**
            mButtonNegativeMessage **=** msg**;**
            **break;**
        **case** DialogInterface**.**BUTTON_NEUTRAL**:**
            mButtonNeutralText **=** text**;**
            mButtonNeutralMessage **=** msg**;**
            **break;**
    **}**
**}***
```

*因此，OnClickListener 被包装在一个消息中，并被设置为 alert controller . mbuttonpositivemessage。*

```
***private** **final** View**.**OnClickListener mButtonHandler **=** **new** View**.**OnClickListener**()** **{**
    @Override **public** **void** **onClick(**View v**)** **{**
        **final** Message m**;**
        **if** **(**v **==** mButtonPositive **&&** mButtonPositiveMessage **!=** **null)** **{**
            m **=** Message**.**obtain**(**mButtonPositiveMessage**);**
        **}** **else** **if** **(**v **==** mButtonNegative **&&** mButtonNegativeMessage **!=** **null)** **{**
            m **=** Message**.**obtain**(**mButtonNegativeMessage**);**
        **}** **else** **if** **(**v **==** mButtonNeutral **&&** mButtonNeutralMessage **!=** **null)** **{**
            m **=** Message**.**obtain**(**mButtonNeutralMessage**);**
        **}** **else** **{**
            m **=** **null;**
        **}**
        **if** **(**m **!=** **null)** **{**
            m**.**sendToTarget**();**
        **}**
        *// Post a message so we dismiss after the above handlers are executed.*
        mHandler**.**obtainMessage**(**ButtonHandler**.**MSG_DISMISS_DIALOG**,** mDialogInterface**)**
                **.**sendToTarget**();**
    **}**
**};***
```

*请注意:m = message . obtain(mButtonPositiveMessage)。*

*消息被克隆，其副本被发送。这意味着原始消息永远不会被发送，因此永远不会被回收。所以它会永远保存它的内容，直到垃圾被收集。*

*现在让我们假设，由于 HandlerThread 本地引用，该消息在从回收池获得之前已经泄漏了。该对话框最终被垃圾回收，并释放对 mButtonPositiveMessage 保存的消息的引用。*

*然而，由于消息已经泄漏，它不会被垃圾收集。它的内容、OnClickListener 以及活动也是如此。*

# *犯罪的确凿证据*

*我们能证明我们的理论吗？*

*我们需要向 HandlerThread 发送一条消息，让它被消费和回收，而不要向那个线程发送任何其他消息，以免它泄漏最后一条消息。然后，我们需要显示一个带有按钮的对话框，并希望这个对话框将从池中获得相同的消息。这很有可能发生，因为一旦回收，消息将成为池中的第一个。*

```
*HandlerThread background **=** **new** **HandlerThread(**"BackgroundThread"**);**
background**.**start**();**
Handler backgroundhandler **=** **new** **Handler(**background**.**getLooper**());**
**final** DialogInterface**.**OnClickListener clickListener **=** **new** DialogInterface**.**OnClickListener**()** **{**
  @Override **public** **void** **onClick(**DialogInterface dialog**,** **int** which**)** **{**
    MyActivity**.**this**.**makeCroissants**();**
  **}**
**};**
backgroundhandler**.**post**(new** **Runnable()** **{**
  @Override **public** **void** **run()** **{**
    runOnUiThread**(new** **Runnable()** **{**
      @Override **public** **void** **run()** **{**
        **new** AlertDialog**.**Builder**(**MyActivity**.**this**)** *//*
            **.**setPositiveButton**(**"Baguette"**,** clickListener**)** *//*
            **.**show**();**
      **}**
    **});**
  **}**
**});***
```

*如果我们运行上面的代码，然后旋转屏幕来破坏活动，这个活动很有可能会泄漏。*

*LeakCanary 正确地检测到泄漏:*

```
** GC ROOT thread android.os.HandlerThread.<Java Local> (named 'BackgroundThread')
* references android.os.Message.obj
* references com.example.MyActivity$1.this$0 (anonymous class implements android.content.DialogInterface$OnClickListener)
* leaks com.example.MyActivity instance*
```

*既然我们已经正确地再现了它，让我们看看我们能做些什么来修复它。*

# *启动修复*

*只支持使用 ART VM 的设备，即 Android 5+。不再有虫子了！还有，没有更多的用户。*

# *这个修不好*

*您还可以假设这些泄漏的影响有限，您有更好的事情要做，或者可能有更简单的泄漏要修复。LeakCanary 默认忽略所有 message leaks。但是要注意，一个活动在内存中保存了它的整个视图层次结构，可以保留几兆字节。*

# *应用程序修复*

*确保您的对话界面。OnClickListener 实例不保存对活动实例的强引用，例如，在分离对话框窗口时清除对侦听器的引用。这里有一个包装类来简化它:*

```
***public** **final** **class** **DetachableClickListener** **implements** DialogInterface**.**OnClickListener **{** **public** **static** DetachableClickListener **wrap(**DialogInterface**.**OnClickListener delegate**)** **{**
    **return** **new** **DetachableClickListener(**delegate**);**
  **}** **private** DialogInterface**.**OnClickListener delegateOrNull**;** **private** **DetachableClickListener(**DialogInterface**.**OnClickListener delegate**)** **{**
    **this.**delegateOrNull **=** delegate**;**
  **}** @Override **public** **void** **onClick(**DialogInterface dialog**,** **int** which**)** **{**
    **if** **(**delegateOrNull **!=** **null)** **{**
      delegateOrNull**.**onClick**(**dialog**,** which**);**
    **}**
  **}** **public** **void** **clearOnDetach(**Dialog dialog**)** **{**
    dialog**.**getWindow**()**
        **.**getDecorView**()**
        **.**getViewTreeObserver**()**
        **.**addOnWindowAttachListener**(new** **OnWindowAttachListener()** **{**
          @Override **public** **void** **onWindowAttached()** **{** **}**
          @Override **public** **void** **onWindowDetached()** **{**
            delegateOrNull **=** **null;**
          **}**
        **});**
  **}**
**}***
```

*然后您可以包装所有 OnClickListener 实例:*

```
*DetachableClickListener clickListener **=** wrap**(new** DialogInterface**.**OnClickListener**()** **{**
  @Override **public** **void** **onClick(**DialogInterface dialog**,** **int** which**)** **{**
    MyActivity**.**this**.**makeCroissants**();**
  **}**
**});**AlertDialog dialog **=** **new** AlertDialog**.**Builder**(this)** *//*
    **.**setPositiveButton**(**"Baguette"**,** clickListener**)** *//*
    **.**create**();**
clickListener**.**clearOnDetach**(**dialog**);**
dialog**.**show**();***
```

# *水管工的修理*

*定期刷新工作线程:当处理线程空闲时发送一条空消息，以确保没有消息长时间泄漏。*

```
***static** **void** **flushStackLocalLeaks(**Looper looper**)** **{**
  **final** Handler handler **=** **new** **Handler(**looper**);**
  handler**.**post**(new** **Runnable()** **{**
    @Override **public** **void** **run()** **{**
      Looper**.**myQueue**().**addIdleHandler**(new** MessageQueue**.**IdleHandler**()** **{**
        @Override **public** **boolean** **queueIdle()** **{**
          handler**.**sendMessageDelayed**(**handler**.**obtainMessage**(),** 1000**);**
          **return** **true;**
        **}**
      **});**
    **}**
  **});**
**}***
```

*这对库很有用，因为你不能控制开发人员要用对话框做什么。我们在 Picasso 中使用了它，并为其他类型的工作线程做了类似的[修复](https://github.com/square/picasso/pull/932)。*

# *结论*

*正如我们所看到的，一个微妙的和意想不到的虚拟机行为可能会造成一个小的泄漏，最终占用大量内存，最终导致您的应用程序崩溃，出现 out of memory 错误。一个小漏洞会使一艘大船沉没。*

*非常感谢[乔希·汉弗莱斯](https://twitter.com/jhumphries_sq)、[杰西·威尔森](https://twitter.com/jessewilson)、[马尼克·苏尔塔尼](https://twitter.com/maniksurtani)和[沃特·科卡耶茨](https://twitter.com/WouterCoekaerts)在我们的内部邮件线程中给予的帮助。*

*[](https://twitter.com/Piwai) [## 皮埃尔-伊夫·里考(@皮瓦伊)|推特

### Pierre-Yves Ricau (@Piwai)的最新推文。安卓贝克@广场。巴黎/旧金山

twitter.com](https://twitter.com/Piwai)*