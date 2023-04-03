# 使用 TypeScript 创建任务队列

> 原文：<https://medium.com/quick-code/creating-a-task-queue-with-typescript-3993ed2cc303?source=collection_archive---------1----------------------->

![](img/8b71605147ec788b3fcbaaf5ef6cbbc1.png)

Photo by [Michał Parzuchowski](https://unsplash.com/photos/1O77vgBVkXQ?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/queue?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

一直想写一个超级基础的任务队列。我没有任何特定的语言来实现它，所以我决定使用 TypeScript。

在我们继续之前，我只想说下面的代码是不整洁的，它并不总是使用类型。例如，保存所有队列项的变量应该有一个类型，但它只是一个普通的 JS 对象。

好了，让我们进入代码。

# 长队

我将从队列类开始。这是非常基本的。它有一个属性和两个方法。

该属性保存所有的任务或我称之为“queueItems”。这只是一个简单的空白 JavaScript 对象。

代码的下一部分是“addQueueItemForTopic”函数。它所做的就是接受一个 IQueueItem 类型的 queueItem(我们稍后会讲到)和一个主题。主题只是一个字符串，它将是“queueItems”属性中的一个键。queueItem 参数将是需要运行的任务。

下一个函数是“processItemsForQueueTopic”。这基本上做到了它所说的。它只是为队列当前拥有的每个任务或“queueItem”运行代码。

```
class Queue
{
   queueItems = {};

   addQueueItemForTopic(*queueItem*: IQueueItem, *topic*: string)
   {
      if (this.queueItems[*topic*] === undefined)
      {
         this.queueItems[*topic*] = [*queueItem*];
      }
      else
      {
         this.queueItems[*topic*].push(*queueItem*);
      }
   }

   processItemsForQueueTopic(*topic*: string)
   {
      for (let item of *queue*.queueItems[*topic*])
      {
         item.main();
      }

      this.queueItems[*topic*] = [];
      let numberOfItemsLeft = this.queueItems[*topic*].length;

      if (numberOfItemsLeft > 0)
      {
         console.log("Number of items left in Queue for topic" + *topic*, this.queueItems[*topic*].length);
      }
      else
      {
         console.log("No more tasks for topic " + *topic*);
      }
   }
}

let *queue* = new Queue();
```

# 队列项

现在我们进入“IQueueItem”界面。

```
// Interface that an item added to the queue needs to conform to
interface IQueueItem
{
   main<T>(something?:T);
}
```

这就是“processItemsForQueueTopic”函数能够运行的原因。添加到队列中的每个项目都需要符合这个协议。这个接口有一个叫做“main”的方法。当您创建一个符合这个接口的对象时，您想要进行的所有处理都将发生在这个“main”函数中。

当调用“processItemsForQueueTopic”函数时，它将遍历您指定的主题队列中的每个项目，并调用“queueItem”或任务的“main”函数。

# 创建任务

创建任务非常简单。它只需要符合 IQueueItem 接口。

```
let *task1*: IQueueItem = {
   main: function<T>(something?:T)
   {
      let *calculation* = 1 + 1;
      console.log(*calculation*);
   }
}
```

该任务需要符合 IQueueItem 接口的原因是，它需要具有您在上面看到的 main 函数。

该函数将包含您需要处理的所有逻辑。

然后当您告诉队列处理特定主题的项目时。它将运行该主题的所有项目，然后在每个任务上调用 main 函数，该函数将执行您希望它执行的所有处理。

另一项任务的示例，但以“电子邮件”为主题:

```
let *task2*: IQueueItem = {
   main: function<T>(something?:T)
   {
      let *email* = "test@email.com";
      console.log("Send mail to:", *email*);
   }
}
```

# 添加到队列中

现在需要做的就是将它添加到队列中，如下所示:

```
queue.addQueueItemForTopic(*task1*, "calculation");
queue.addQueueItemForTopic(*task2*, "email");
```

这就是将它添加到队列中所需要做的全部工作。之后，我们将只告诉队列处理或调用主函数。

# 处理队列中的任务

```
queue.processItemsForQueueTopic("calculation");
queue.processItemsForQueueTopic("email");
```

当我们想要处理每个主题的所有任务时，我们只需调用 processItemsForQueue 函数来处理我们想要运行的主题，这样就完成了。

这将运行我们添加到队列中的所有任务。

请注意，这是队列的一个非常基本的版本。可以添加许多其他功能来使它变得更好，更实用。

请在评论中告诉我是否有我可以用这段代码改进的地方！