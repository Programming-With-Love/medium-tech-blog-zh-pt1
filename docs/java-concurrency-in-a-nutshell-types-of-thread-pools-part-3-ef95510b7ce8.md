# Java 并发性概述:线程池的类型(第 3 部分)

> 原文：<https://medium.com/quick-code/java-concurrency-in-a-nutshell-types-of-thread-pools-part-3-ef95510b7ce8?source=collection_archive---------2----------------------->

# 介绍

在之前的文章中，我们讨论了线程池的基础知识。我们已经提供了**执行器服务、可调用、未来和线程池执行器**的概述。在本文中，我们将研究 Java 提供的不同类型的内置和常用线程池。

![](img/79edd1c9c923543ca47493955e25aa42.png)

Photo by [Tim Johnson](https://unsplash.com/photos/Vwf8q3RzBRE?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/fixed-no-of-items?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

## 固定线程池

这是一个通用线程池，其中线程池使用用户提供的线程数(nThreads)进行初始化。在任何时间点，这个线程池都包含 n 个线程。它在内部管理一个 **LinkedBlockingQueue** ，当池中的所有线程都忙并且新任务不能立即处理时，它将额外的任务流入排队。

通过调用 **Executors** 类的 **newFixedThreadPool()** 可以创建一个固定线程池。在下面的代码片段中，创建了一个包含 10 个线程的线程池。

```
ExecutorService executorService = Executors.newFixedThreadPool(10);
```

一旦线程池被创建，任何数量的**可运行的**或**可调用的**任务都可以提交给它进行处理。

```
executorService.submit(() -> {
    System.out.println("Hello world");
});
```

所有任务完成后，池仍将处于活动状态，直到被显式关闭。Java 提供了两种关闭方法来关闭池。

对池调用 **shutdown** ()将阻止池接受新任务，但允许当前运行的任务完成。调用 **shutdownow()** 会阻止接受新任务，并试图取消当前正在运行的任务。

下面的代码片段描述了固定线程池的用法:

Java 提供了两个实现来创建一个固定的线程池。我们已经看到了默认的线程池，用户只需要提供创建线程池所需的线程数量。在这个实现中，Java 用它的内部线程工厂创建线程。

在另一个实现中，Java 为用户提供了自定义 **ThreadFactory** 实现来定制线程创建。当用户想要定制线程创建的某些方面时，这个版本是有用的。例如，用户想要改变线程组、线程池命名模式、线程优先级等等。

以下示例显示了自定义应用程序线程和自定义应用程序线程工厂的实现:

Custom Application Thread

以下代码片段描述了自定义线程工厂的创建:

Custom Thread Factory

以下示例显示了固定线程与自定义线程工厂实现的用法:

Fixed thread pool with custom thread factory

## 缓存线程池

缓存线程池是 Java 提供的线程池的另一种变体，用于一般用途。当提交进行处理的任务不应该等待并且需要在提交后立即处理时，缓存线程池非常有用。为了满足这个需求，如果池中没有线程来处理任务，Java 会为提交的任务创建一个新线程。一个缓存线程池最多可以有 2 个线程。实际上，这个池是用于短期异步任务的。

如果池中没有活动线程，缓存线程池使用一个 **SynchronousQueue** 来缓冲任务。一旦提交了新任务，就会产生一个新线程，并使用提交的任务进行处理。在 SynchronousQueue 中，每个插入操作都必须等待另一个线程的相应移除操作，反之亦然。同步队列没有任何内部容量，甚至没有一个容量。这意味着一旦任务被提交，线程就需要消耗该任务。缓存线程池中的线程在终止之前可以空闲 60 秒。

通过调用 **Executors** 类的 **newCachedThreadPool()** 创建一个缓存线程池。

```
ExecutorService executorService = Executors.newCachedThreadPool();
```

我们还可以提供自定义线程工厂类来覆盖 Java 默认线程工厂，并提供我们自己的实现:

```
ExecutorService executorService = Executors.newCachedThreadPool(new AppThreadFactory(CUSTOM_CACHED_POOL));
```

以下示例显示了默认和自定义线程工厂实现的线程池的用法:

Cached thread pool example

具有自定义线程工厂的缓存线程池:

Cached thread pool with custom thread factory

## 单线线程池

通常，它需要启动并运行一个线程来处理提交的任务。这可以通过使用固定线程池执行器，将池中的线程数设置为 1 来完成。然而，Java 已经为此提供了一个内置的线程池。在 Singlethreadpool 中，池最多可以有一个线程，所有提交的作业按提交顺序执行。