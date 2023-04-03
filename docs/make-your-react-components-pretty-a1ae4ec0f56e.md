# 让你的反应组件漂亮💅

> 原文：<https://medium.com/walmartglobaltech/make-your-react-components-pretty-a1ae4ec0f56e?source=collection_archive---------0----------------------->

![](img/2449053ff95f4ac221436bbcf7f12544.png)

I took this picture. With my phone. Art.

在[沃尔玛实验室](http://www.walmartlabs.com/)我们做了大量的同行代码评审，这很棒，因为我看到了各种聪明的方法来编写来自各种天才工程师的 React 组件。在这篇文章中，我将分享我最喜欢的五种模式和捷径。

但是首先…

## 为什么漂亮的代码更好

有不止一种方法来剥一只猫的皮，也有不止一种方法来编写一个 React 组件。

![](img/867f68c15e17a5be2b4b9078e7bf55dd.png)

然而，仅仅因为你*可以*以任何你认为合适的方式编写组件，并不意味着你*应该*。代码是要被人类阅读的；计算机只是解释你扔给它们的任何杂乱无章的字符。🤖

如果你让你的组件内部和外部一样漂亮，你的开发同行会感谢你的。

漂亮的组件…

*   💡即使没有注释,**容易理解吗**
*   🚀甚至比笨重的代码更有性能
*   🐛增加**在 bug**到达 QA 之前捕获它们的机会
*   📝**是否简洁**并以少言多

> “少即是多。”
> 
> 密斯·凡·德·罗

# 1.无状态功能组件(SFC)

我最喜欢的 React 组件优化，也是经常被忽略的，是无状态功能组件，简称 SFC。我喜欢 sfc，因为它们大大减少了扩展 React 组件类带来的负担，并将提供**性能优势作为额外的好处！

基本上，SFC 会让你的应用程序运行得更快，看起来更棒。

![](img/bcef63286df1cf4fe0e047f065427d8b.png)

The Batmobile is an example of a Stateless Functional Component.

* *在幕后，sfc 只是被包装在一个具有唯一渲染功能的简单组件中，但这种模式很快将能够受益于额外的优化:

> 未来，我们还将能够通过避免不必要的检查和内存分配，针对[sfc]进行性能优化。
> 
> —本·阿尔珀特，[反应博客](https://facebook.github.io/react/blog/2015/09/10/react-v0.14-rc1.html#stateless-function-components)

## 实际例子

让我们来看看 SFC 如何大大减少编写简单的相关搜索组件所需的代码量:

![](img/933f51150ffe5525e3a835c8e1a91471.png)

即使作为一个传统的反应。组件扩展，显示相关搜索的列表并不需要太多时间:

```
export default class RelatedSearch extends React.Component {
  constructor(props) {
    super(props); this._handleClick = this._handleClick.bind(this);
  } _handleClick(suggestedUrl, event) {
    event.preventDefault();
    this.props.onClick(suggestedUrl);
  } render() {
    return (
      <section className="related-search-container">
        <h1 className="related-search-title">Related Searches:</h1>
        <Layout x-small={2} small={3} medium={4} padded={true}>
          {this.props.relatedQueries.map((query, index) =>
            <Link
              className="related-search-link"
              onClick={(event) =>
                this._handleClick(query.searchQuery, event)}
              key={index}>
              {query.searchText}
            </Link>
          )}
        </Layout>
      </section>
    );
  }
}
```

然而，SFC 替代方案提供了 29%的代码节省**！**

```
const _handleClick(suggestedUrl, onClick, event) => {
  event.preventDefault();
  onClick(suggestedUrl);
};const RelatedSearch = ({ relatedQueries, onClick }) =>
  <section className="related-search-container">
    <h1 className="related-search-title">Related Searches:</h1>
    <Layout x-small={2} small={3} medium={4} padded={true}>
      {relatedQueries.map((query, index) =>
        <Link
          className="related-search-link"
          onClick={(event) =>
            _handleClick(query.searchQuery, onClick, event)}
          key={index}>
          {query.searchText}
        </Link>
      )}
    </Layout>
  </section>export default RelatedSearch;
```

这种惊人的代码减少主要通过两种方式实现，这源于 ES2015 类扩展的缺失:

*   无构造器(5 个位置)
*   使用箭头语法的简明呈现语句(4 LOC)

然而，SFC 真正伟大的地方是可读性大大提高了。首先，**sfc 以尽可能好的方式代表**[](/@dan_abramov/smart-and-dumb-components-7ca2f9a7c7d0#.5sgngec83)****纯组件，高度关注返回组件的 JSX。移除不再需要的构造函数，并在组件外部重构 *_handleClick()* 函数也有助于提高可读性，因为关注点被更清晰地分离了。****

****SFC 中我最喜欢的部分之一是如何使用 ES2015 [对象析构](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment#Object_destructuring)语法在函数参数列表的顶部清晰地定义属性:****

```
**const RelatedSearch = (**{ relatedQueries, onClick }**) =>**
```

****这是一个方便的地方，可以快速查看组件接收到的所有属性，也是一种避免到处引用 this.props 的方法，使代码整体上更容易阅读。****

****![](img/52f43802c178265d1d834f8def716b0d.png)****

## ****什么时候可以使用 sfc？****

****大概 sfc 最好的部分是**它们可以在任何可以使用纯组件的地方使用**。在沃尔玛实验室，我们使用 Redux 来管理应用程序的状态，这意味着我们几乎所有的组件都是 sfc 的候选者。****

****但是，在两种情况下，无状态组件不能是 SFC:****

*   ****当需要组件生命周期方法时****
*   ****当引用被使用时****

****虽然这两种情况相当罕见，但通过仔细的架构和规划，通常可以完全避免。****

# ****2.条件成分****

****JSX 不允许 if 语句，所以为了避免仅仅为了使用条件逻辑而将代码重构为子模块，请尝试三元组:****

```
**render() {
  <div class="search-results-container">
    {this.props.isGrid
      ? <SearchResultsGrid />
      : <SearchResultsList />}
  </div>
}**
```

****如果满足或不满足某个条件，这个表达式在返回一个或另一个组件方面非常棒。但是，对于组件应该呈现还是不呈现的情况，三元组可能不是最佳选择:****

```
**render() {
  <div class="search-results-list">
    {this.props.isSoftSort
      ? <SoftSortBanner />
      : null
    }
  </div>
}**
```

****这是可行的，但是这就像告诉 React“渲染这个组件，否则！”****

****![](img/ac92c9d2d4ae3fc36baa3c0cb1df32b7.png)****

****一种更具语义且更简洁的方法是使用逻辑 and 双&符号返回条件分量或 false:****

```
**render() {
  <div class="search-results-list">
    {!!this.props.isSoftSort && <SoftSortBanner />}
  </div>
}**
```

****这当然取决于个人偏好，有些人更喜欢任何条件成分的三元方法。****

****编辑——在这篇文章的前一个版本中，我省略了布尔型转换！！*来自左边的操作数，这是很危险的，因为一些假值比如*零*可能会被 React 无意中渲染。使用* & & *时，始终转换左侧操作数。感谢 Reddit 用户*[*miketa 1957*](https://www.reddit.com/r/reactjs/comments/54uez2/heres_5_useful_patterns_to_make_your_component/d859jq2)*[*HumansAreDumb*](https://www.reddit.com/r/reactjs/comments/54uez2/heres_5_useful_patterns_to_make_your_component/d85aufz)*[*Alex Barrett*](https://www.reddit.com/r/reactjs/comments/54uez2/heres_5_useful_patterns_to_make_your_component/d85jmfm)*指出这一点。*******

# ******3.React 和 Redux 中的箭头语法******

******ES2015 充满了漂亮的语法快捷键，其中我最喜欢的是[箭头符号](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)。这些在编写组件时特别有用，但是我经常看到它们没有发挥出全部潜力:******

```
****const SoftSort = ({ hardSortUrl, sortByName, onClick }) => {
  return (
    <div className="SearchInfoMessage">
      Showing results sorted by both Relevance and {sortByName}.
      <Link
        href={`?${hardSortUrl}`}
        onClick={(ev) => onClick(ev, hardSortUrl)}>
        Sort results by {sortByName} only
      </Link>
    </div>
  );
};****
```

******这个函数只返回 JSX，所以我们可以更简洁:******

```
****const SoftSort = ({ hardSortUrl, sortByName, onClick }) =>
  <div className="SearchInfoMessage">
    Showing results sorted by both Relevance and {sortByName}.
    <Link
      href={`?${hardSortUrl}`}
      onClick={(ev) => onClick(ev, hardSortUrl)}>
      Sort results by {sortByName} only
    </Link>
  </div>****
```

******这相当于减少了 25%的代码行——现在代码行减少到了 11 行！******

******![](img/4758021c0d807d92e3d09728f56107b1.png)******

******Use Arrow functions the way they were meant to be!******

******另一个我经常看到箭头函数没有充分发挥潜力的地方是在 Redux *mapStateToProps* 函数中:******

```
****const mapStateToProps = ({isLoading}) => {
  return ({
    loading: isLoading,
  });
};****
```

******但是在这里要小心，当返回一个对象时，你必须用括号把它括起来:******

```
****const mapStateToProps = ({isLoading}) => ({
  loading: isLoading
});****
```

*******编辑—感谢*[*Victor Storozhenko*](https://medium.com/u/14bb25afc42b?source=post_page-----a1ae4ec0f56e--------------------------------)*指出本例中对象析构的改进。*******

******优化箭头函数的目的是**通过关注函数**的重要部分并移除不必要的返回语句和括号的背景噪音来提高可读性。******

# ******4.说出你对对象析构和传播属性的理解******

******大型构件经常患有***综合症****。也就是说，你会看到短语 *this.props* 到处都是，不幸的是，它作为额外噪音的来源降低了可读性。幸运的是，有了 ES2015 对象析构，我们有了一种在 JSX 中简洁地编写道具的方法。举一个产品价格组件的例子，它有许多道具要呈现:*******

```
****render() {
  return (
    <ProductPrice
      hidePriceFulfillmentDisplay=
       {this.props.hidePriceFulfillmentDisplay}
      primaryOffer={this.props.primaryOffer}
      productType={this.props.productType}
      productPageUrl={this.props.productPageUrl}
      inventory={this.props.inventory}
      submapType={this.props.submapType}
      ppu={this.props.ppu}
      isLoggedIn={this.props.isLoggedIn}
      gridView={this.props.isGridView}
    />
  );
}****
```

******哇，好多这种道具啊！这里有一个减少*噪音的方法:*******

```
****render() {
  const {
    hidePriceFulfillmentDisplay,
    primaryOffer,
    productType,
    productPageUrl,
    inventory,
    submapType,
    ppu,
    isLoggedIn,
    gridView
  } = this.props; return (
    <ProductPrice
      hidePriceFulfillmentDisplay={hidePriceFulfillmentDisplay}
      primaryOffer={primaryOffer}
      productType={productType}
      productPageUrl={productPageUrl}
      inventory={inventory}
      submapType={submapType}
      ppu={ppu}
      isLoggedIn={isLoggedIn}
      gridView={isGridView}
    />
  );
}****
```

******这增加了几行代码，但也使我们的代码更容易阅读，并且**清楚地指出我们在组件**中使用了哪些道具。在大型组件中这样做是有意义的，因为相同的属性可能会在多个子组件中多次使用，但是在这个简单的例子中，我们可以使用 [Spread Attributes](https://facebook.github.io/react/docs/jsx-spread.html#spread-attributes) 作为超级快捷方式:******

```
****render() {
  const props = this.props;
  return <ProductPrice {...props} />
}****
```

******无状态功能组件(如前所述)大量使用对象析构，因为它们接收 props 作为参数:******

```
****const ProductPrice = ({
  hidePriceFulfillmentDisplay,
  primaryOffer,
  productType,
  productPageUrl,
  inventory,
  submapType,
  ppu,
  isLoggedIn,
  gridView
}) =>
  <ProductPrice
    hidePriceFulfillmentDisplay={hidePriceFulfillmentDisplay}
    primaryOffer={primaryOffer}
    productType={productType}
    productPageUrl={productPageUrl}
    inventory={inventory}
    submapType={submapType}
    ppu={ppu}
    isLoggedIn={isLoggedIn}
    gridView={isGridView}
  />****
```

# ******5.方法定义速记******

******这个技巧可能不是非常有用，但它确实很漂亮。💅******

******无论何时你在一个对象中编写方法，你都可以使用 ES2015 [方法定义简写](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Method_definitions)，而不是枯燥的旧 ES5 做事方式。这在 *defaultProps* 声明中特别方便:******

```
****Link.defaultProps = {
  onClick(event) {
    event.preventDefault();
    Logger.log(event);
  }
};****
```

******另外，如果您只想设置一个默认的无操作方法，这非常简单:******

```
****ProductRating.defaultProps = {
  onStarsClick() {}
};****
```

# ******最后******

******我希望我分享的这些技巧能让你的 React 应用程序变得更漂亮，并且你会积极思考如何让你的代码更漂亮。记住，**漂亮的代码做了很多不用多说**。******

> ******“说话轻声细语，拿着大棒。”******
> 
> ******—西奥多·罗斯福******