# Mendix 中的响应式表之路

> 原文：<https://medium.com/mendix/my-story-6d96d1aeca11?source=collection_archive---------0----------------------->

![](img/37dcdf8a28f93ff293fc16e12625edd5.png)

在这篇博文中，我介绍了在 Mendix 中仅使用标准的 Mendix 小部件和 CSS 构建好看的响应式表格的不同方法。首先，让我们看看数据网格，这是在 Mendix 中创建表的标准方式。

# 数据网格

这个小部件非常适合静态数据。设置起来非常快，只需将小部件连接到一个实体，Mendix 就会自动生成所有的列和搜索字段。这是一个很棒的小部件，我经常使用它，尤其是在管理页面上，看起来感觉不是主要考虑的。也就是说，它不是非常可定制的。例如，没有办法有一个内嵌按钮，至少没有一些黑客攻击。要解决这个问题，可以尝试将列表视图与表格结合使用。

![](img/5b8d294af2061f8623a43df8674d2def.png)

# 列表视图+表格

与数据网格不同，[列表视图](https://docs.mendix.com/refguide/list-view)适用于大多数小部件，例如按钮、日期选择器等。使用一点 CSS，标题和列表视图可以看起来像一个表格。在这里看它的行动[。](https://cssflexandgrid-sandbox.mxapps.io/p/table)

这种方法的一个真正令人讨厌的问题是列的大小被指定了两次:一次用于标题，一次用于正文。这与 Mendix 所做的半自动列宽计算相结合，使得更改任何与列相关的内容都很困难。

[视频](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/f2d07d2d-6d47-4ec8-9c29-7e20160899bd/Jun_7_2019_2_20_PM_%281%29_%28online-video-cutter.com%29_%281%29.mp4?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Credential=ASIAT73L2G45AEMTJ7PR%2F20200131%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20200131T122617Z&X-Amz-Expires=86400&X-Amz-Security-Token=IQoJb3JpZ2luX2VjEGkaCXVzLXdlc3QtMiJIMEYCIQDOweaUmXvV5xb9wOkftrCi%2BKHv%2B5rutgaeMPM8F7D3BgIhAJGOglvvRGRDSNZo6z8cLq5lMK8icNXzHnLiUFo9kG5bKrQDCBIQABoMMjc0NTY3MTQ5MzcwIgwnr%2Fn3rtOkYai%2FNXwqkQPa01mCvxku4O8DWYbvW%2Fit7AWHys%2F%2BygxhSrtbcSc4swAWaKt7rFYwxOl4kkhtK1ZP51Zda1uzdBacDA1MiDYiRARcjVxUykFN6cm5zdPakgtK2b6A1ol9Gxa8v06R1FMO8qW4kBIKB3XO%2BmZ5bfpR6nMOMeYJMAwH3Fkd7195C28QQl3nNIrNs5yOSR4EyGpyT3b8NWN1L%2F1cvDSL1SW8SOp4KAsEvhqLT2TDvham6Yd8I7xwpen5ISBjL4TNBtPr%2FaLExngRU9vX9rXNv78N87ZSTmhOLFYotD%2BuFgfInNwQ8Q%2BRbuvvZfVtNBVPXqUDxmqp9kj6cWKw7hDZBNTXzZWmNeYG62XNjx7BCRSlPkol%2B%2FoxLrw9a3Ho1DDNOcXt3SyO6FcYoL26j%2Fww%2BvWVaeC9CLrLhjb4A8Zr10j8lbMlhIcqHwQUiUaDtlKM%2Fw%2Bo%2FLiyNP65JocSqlaBvR1gsP9IoY24L0PG%2F9BImVkvxd17nBSAqGetA0MzCBXAOfRGnWZ9ZDVbNpvpNodZ%2FDLqzTCz28%2FxBTrqAS6UISiXP2G9%2FolUJ6TS5TiTyRtYUv%2BIf0hBnEZDW%2FWqnilkYqdKMIupVlCPGs6CrlVKzmg5zlHvKRM61IBohZuJwtV7JM1KR7GWcYgVYRIwoNqzP76jkUyav77NzJIxqAC2EsrUwGoPzR3BZTiKzlTqLKEDa8JvppN2aTug0efzbowA184i4IHWMZwe5Q%2F%2F7TQd1waTT6R8cW%2B5VCsQlxkwshpgWNwv15cxBYVuNgQC6aLdWv9KZsTXSSo2XlRowBc7yZKk1962v2zDMO5C3Df2PN5g%2B36mQmK8r%2F3wrYcnUDtINkFDwkU3SQ%3D%3D&X-Amz-Signature=e8e5024ca00bebc81a12f7490296ce114eae7ee3d3901d5e810a87c944bd899e&X-Amz-SignedHeaders=host)。

就布局而言，该表支持使用百分比或固定大小的列进行重新调整，其中一个`auto`列填充了所有剩余的可用空间。这很好，但是下面的例子呢:一个表有两个固定大小的列和三个响应列，其中一个应该是另外两个的两倍大。这在表格小部件中是不可能的，但在 *flex* 中是可能的。

# 列表视图+弹性

Flex 是一个 CSS 概念，旨在提供一种简单有效的方式在容器中的项目间分配空间，即使它们的大小未知。使用 flex，可以很容易地定义不同的列大小，如固定或响应最小和最大大小。此外，还可以定义一个列的相对大小，例如，使一个列的大小是另一个列的两倍，同时保持两个列都响应。下面是使用 flex 实现的 Mendix 表。

![](img/b68f1aebeaaa949a10a417065062a42b.png)

注意标题和正文列是如何使用相同的类的。与前面的方法不同，这使得无需重复就可以轻松定义每列的大小。下面是样式表:

```
.flex-table {
    & .flex-table-row, .flex-table-header-row {
        display: flex; // nested HTML elements should be layed out using flex
        width: 100%; // the row should fill out all availble space of the parent element> div {
            padding: 5px;  // some padding for all columns
        }

        & > .column-id { // responsive column with min and max width
            min-width: 150px;
						max-width: 300px;
            flex-grow: 100;
        }
        & > .column-toggle { // fixed size column
            width: 100px;
        }
        & > .column-timestamp { // fixed size column
            width: 150px;
        }
        & > .column-name { // responsive column with min width
            min-width: 100px;
            flex-grow: 100;
        }
        & > .column-description { // responsive and twice as large as column-name
            min-width: 200px;
            flex-grow: 200;
        }
    }
    & .flex-table-header-row > div {
        position: sticky; top: 0;  z-index: 999; // keep header visible while scrolling
        font-weight: bold; background-color: lightgrey; // styling for header cells
    }
}
```

[结果](https://cssflexandgrid-sandbox.mxapps.io/p/flex)是一个响应式表格，其中的列大小是高度可定制的。

尽管 flex 令人惊叹，但它有一个主要问题。也就是说，flex 容器只在一个方向上对齐项目，在我们的例子中是水平对齐，而完全不知道在另一个方向上发生了什么。例如，当一行中的一个单元格包含大量内容，因此需要更多空间时，这会导致问题。该行的 flex 容器将很好地处理这种情况，并为大内容腾出空间。但是，其他行都不会展开，这看起来像是发生了可怕的错误:

因为一行中的 flex 布局不知道其他行，所以很容易导致列不对齐。

![](img/58f11dcf814c5f348205ebc06791f314.png)

为了获得想要的行为，我们需要使用另一个 CSS 概念，叫做*网格*。

# 列表视图+网格

[Grid](https://css-tricks.com/snippets/css/complete-guide-grid/) 是 flex 向二维的延伸。

从使用 flex 到使用 grid 相当简单。Mendix 中的页面结构与 flex 中的相同。就 CSS 而言，定义列大小的方式略有不同。此外，由于 grid 的工作方式，需要一些额外的属性来指示浏览器不要在网格布局中放置 Mendix 生成的元素。

![](img/df675c959c362ce133c095f7f65bf32a.png)

```
.grid-table {
	display: grid; // nested HTML elements should be layed out using grid
  width: 100%; // the table should fill out all availble space of the parent element // definition of the number and size of columns
	grid-template-columns: minmax(150px,15fr) minmax(100px,10fr) minmax(150px,15fr) minmax(100px,10fr) minmax(200px,20fr);
	& .grid-table-row {
	    > div {
	        padding: 5px;  // some padding for all columns
	    }
	} & .grid-table-header > div {
	    position: sticky; top: 0; z-index: 999; // keep header visible while scrolling
	    font-weight: bold; background-color: lightgrey;// styling for header cells
	} // do not place the below elements in the grid
	& .mx-listview, .grid-table-row {        display: contents; 
	    > .mx-list {                         display: contents; 
	        > .mx-listview-item {            display: contents; 
	            > .mx-dataview {             display: contents; 
	                > .mx-dataview-content { display: contents; }
	            }
	        }
	    }
	}
}
```

正如你在这里看到的这张桌子现在可以优雅地重新缩放，没有任何瑕疵。

# 摘要

在 Mendix 中有多种方法来构建表格。当为管理员构建简单的 CRUD 页面时，数据网格可能是最好的方法。然而，当构建面向用户的表格时，我建议使用列表视图和 CSS 网格，因为可以进行定制。可从 [Mendix 应用商店](https://appstore.home.mendix.com/link/app/110094/)下载示例实现。

最后一件事；网格是 CSS 的一个相当新的成员，并不是所有的浏览器都支持所有的网格概念。在决定使用哪种方法时，记住这一点很重要。

感谢你花时间阅读这篇文章，我希望它能帮助你开发出优秀的应用程序！这篇博客最初发布在[这里](https://www.notion.so/The-road-to-responsive-tables-in-Mendix-f8a1de4595634167bd489c48b565fbe1)。