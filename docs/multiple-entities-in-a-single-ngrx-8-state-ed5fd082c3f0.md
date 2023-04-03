# 单个 NgRx 8 状态中的多个实体

> 原文：<https://medium.com/capital-one-tech/multiple-entities-in-a-single-ngrx-8-state-ed5fd082c3f0?source=collection_archive---------2----------------------->

## 如何管理具有复杂数据的复杂状态

![](img/df4b77da3719d1322330ea72fbd9cd5b.png)

[实体](https://ngrx.io/guide/entity)是 [NgRx](https://ngrx.io/) 的一个有用的附加组件。它们通过以规范化状态存储数据来帮助管理大量数据，并提供添加、更新和删除数据对象的有用功能。

随着应用程序变得越来越复杂，您可能会意识到需要将多个实体对象存储在一个状态中。在最近的一个项目中，我发现自己正处于这样的情况:我正在构建一个加载了图表和其他数据可视化元素的 web 应用程序。这些元素是相关的，但不是简单的一对一或一对多的关系。我发现对数据使用单一的 EntityAdapter 会导致复杂和过度工程化的 action reducers。

我对这个混乱的 reducer 的解决方案是将实体对象分解成多个实体。然而，我想保留我创建的状态，迫使我在一个状态中容纳两个 [EntityAdapters](https://ngrx.io/guide/entity/adapter) 。

我将通过一个使用两个对象的简化示例向您展示我是如何做到这一点的:书籍和作者。让我们从定义我们的实体开始:`book.entity.ts`和`author.entity.ts`。

**book.entity.ts**

```
export **function** selectBookId(b: Book): number {
  return b.id;
}export **function** sortByBookTitle(a: Book, b: Book): number {
  return a.title.localeCompare(b.title);
}export **const** bookAdapter: EntityAdapter<Book> = createEntityAdapter<Book>({
  selectId: selectBookId,
  sortComparer: sortByBookTitle
});export **interface** BookState **extends** EntityState<App> {}
```

**author.entity.ts**

```
export **function** selectAuthorId(a: Author): number {
  return a.id;
}export **function** sortByAuthorLastName(a: Author, b: Author): number {
  return a.lastName.localeCompare(b.lastName);
}export **const** authorAdapter: EntityAdapter<Author> =   createEntityAdapter<Author>({
  selectId: selectAuthorId,
  sortComparer: sortByAuthorLastName
});export **interface** AuthorState **extends** EntityState<Author> {}
```

正如您在上面看到的，我有两个相关的实体，但不能存储在单个数据对象中，主要是因为书籍和作者之间的多对多关系，书籍可以由多个作者编写，每个作者可以写多本书。

通常，我们会将函数和 EntityAdapter 合并到 reducer 文件中。然而，由于有多个适配器，我认为最好将它们分离到各自的文件中，这样 reducer 文件就不会变得过于混乱。

既然我们已经定义了实体，我们应该看看我们的状态:

**library.reducer.ts**

```
export **interface** LibraryState {
  books: BookState;
  authors: AuthorState;
  selectedCategory: string;
}export **const** initialState: LibraryState = {
  books: bookAdapter.getInitialState(),
  authors: authorAdapter.getInitialState(),
  selectedCategory: ‘NON-FICTION’,
};
```

状态很简单。它包含图书实体、作者实体和一个选定类别的字段。神奇的事情发生在状态初始化中；我们从两个`*.entity.ts`文件中导入适配器，这样当状态被初始化时，两个实体都用它创建。

最后要记住的是，[实体助手函数](https://ngrx.io/guide/entity/adapter#adapter-collection-methods) (addMany()、update()、upsert()等。)中的[减速器](https://ngrx.io/guide/store/reducers)也需要修改。下面是一个 StoreAuthors 操作的示例，它将 Authors 的有效负载添加到状态中。

**library.reducer.ts**

```
…
on(LibraryActions.storeAuthors, (state, { authors }) **=>** (
  {
    …state,
    authors: authorAdapter.addMany(authors, state.authors)
  }
)),
…
```

reducer 将 LibraryState 从 Author 实体中分离出来，这就是为什么我们使用 [spread 操作符](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Spread_syntax)来存储状态，并独立修改`authors` 。此外，所有适配器的第二个函数参数是状态。这里我们不传入 LibraryState 对象；相反，我们传递的是独占作者实体的状态片段。

通过将实体分开，我们完成了几件事情。首先，我们将一个潜在的复杂实体分解成两个相关但独立的实体。其次，我们通过解开实体来简化父状态。最后，我们简化了 reducer 函数，减少了不必要的状态更新。

这就是将多个 NgRx 实体合并到一个州的全部内容！希望这篇教程对你的工作有所帮助！

*披露声明:2019 资本一。观点是作者个人的观点。除非本帖中另有说明，否则 Capital One 不隶属于所提及的任何公司，也不被这些公司认可。使用或展示的所有商标和其他知识产权是其各自所有者的财产。*