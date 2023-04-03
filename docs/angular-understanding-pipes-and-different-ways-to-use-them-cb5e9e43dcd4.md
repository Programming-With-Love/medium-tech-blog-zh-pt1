# Angular 了解管道和使用管道的不同方式

> 原文：<https://medium.com/bb-tutorials-and-thoughts/angular-understanding-pipes-and-different-ways-to-use-them-cb5e9e43dcd4?source=collection_archive---------4----------------------->

## 用实例探索角形管道

有时，我们希望在将数据放入模板之前，通过编辑来更改来自 API 或其他来源的数据。如果数据是一个组件的私有数据，可以在组件中进行编辑。如果我们不得不在应用程序的许多组件中这样做呢？

在应用程序中的多个地方这样做并不是最好的做法。在这些情况下，角形管道是一种很好的方式。管道接收输入，并以所需的方式进行转换，然后以…