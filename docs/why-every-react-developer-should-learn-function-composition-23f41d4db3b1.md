# 为什么每个 React 开发人员都应该学习函数组合

> 原文：<https://medium.com/javascript-scene/why-every-react-developer-should-learn-function-composition-23f41d4db3b1?source=collection_archive---------0----------------------->

![](img/93bf2ff569ca1d1e51001517e7d8841d.png)

假设您正在构建一个 React 应用程序。在应用程序的每一个页面视图中，你都有很多事情要做。

*   检查并更新用户验证状态
*   检查当前活动的特征以决定呈现哪些特征(连续交付需要)
*   记录每个页面组件装载
*   呈现标准布局(导航、侧边栏等)

像这样的事情通常被称为横切关注点。起初，你不会这样想他们。您只是厌倦了将一堆样板代码复制粘贴到每个组件中。类似于:

```
const MyPage = ({ user = {}, signIn, features = [], log }) => {
  // Check and update user authentication status
  useEffect(() => {
    if (!user.isSignedIn) {
      signIn();
    }
  }, [user]); // Log each page component mount
  useEffect(() => {
    log({
      type: 'page',
      name: 'MyPage',
      user: user.id,
    });
  }, []); return <>
    {
      /* render the standard layout */
      user.isSignedIn ?
        <NavHeader>
          <NavBar />
          {
            features.includes('new-nav-feature')
            && <NewNavFeature />
          }
        </NavHeader>
          <div className="content">
            {/* our actual page content... */}
          </div>
        <Footer /> :
        <SignInComponent />
    }
  </>;
};
```

我们可以通过将所有这些东西抽象成独立的提供者组件来消除这些弊端。那么我们的页面可能看起来像这样:

```
const MyPage = ({ user = {}, signIn, features = [], log }) => {
  return (
    <>
      <AuthStatusProvider>
        <FeatureProvider>
          <LogProvider>
            <StandardLayout>
              <div className="content">{/* our actual page content... */}</div>
            </StandardLayout>
          </LogProvider>
        </FeatureProvider>
      </AuthStatusProvider>
    </>
  );
};
```

尽管如此，我们仍然有一些问题。如果我们的标准横切关注点发生变化，我们需要在每一页上改变它们，并保持所有页面同步。我们还必须记住向每个页面添加提供者。

# 高阶组件

更好的解决方案是使用高阶组件(HOC)来包装我们的页面组件。这是一个接受一个组件并返回一个新组件的函数。新组件将呈现原始组件，但具有一些附加功能。我们可以用它来包装我们的页面组件和我们需要的所有提供者。

```
const MyPage = ({ user = {}, signIn, features = [], log }) => {
  return <>{/* our actual page content... */}</>;
};const MyPageWithProviders = withProviders(MyPage);
```

让我们看看我们的日志记录器作为一个特设的样子:

```
const withLogger = (WrappedComponent) => {
  return function LoggingProvider ({ user, ...props }) {
    useEffect(() => {
      log({
        type: 'page',
        name: 'MyPage',
        user: user.id,
      });
    }, []); return <WrappedComponent {...props} />;
  };
};
```

# 功能组成

为了让我们所有的提供者一起工作，我们可以使用函数组合将它们组合成一个单独的 HOC。功能组合是将两个或多个功能组合起来产生一个新功能的过程。这是一个非常强大的概念，可以用来构建复杂的应用程序。

函数组合是将一个函数应用于另一个函数的返回值。在代数中，它由函数复合运算符来表示:∘

```
(f ∘ g)(x) = f(g(x))
```

在 JavaScript 中，我们可以创建一个名为`compose`的函数，并用它来组成更高阶的组件:

```
const compose = (...fns) => (x) => fns.reduceRight((y, f) => f(y), x);const withProviders = compose(
  withUser,
  withFeatures,
  withLogger,
  withLayout
);export default withProviders;
```

现在你可以在任何需要的地方导入`withProviders`。不过，我们还没完。大多数应用程序都有很多不同的页面，不同的页面有时会有不同的需求。例如，我们有时不想显示页脚(例如，在有无限内容流的页面上)。

# 函数 Currying

curried 函数是一个一次接受多个参数的函数，通过返回一系列函数，每个函数接受下一个参数。

```
// Add two numbers, curried:
const add = (a) => (b) => a + b;// Now we can specialize the function to add 1 to any number:
const increment = add(1);
```

这是一个简单的例子，但是 currying 有助于函数合成，因为一个函数只能返回一个值。如果我们想定制布局函数来获取额外的参数，最好的解决方案是 curry。

```
const withLayout = ({ showFooter = true }) =>
  (WrappedComponent) => {
    return function LayoutProvider ({ features, ...props}) {
      return (
        <>
          <NavHeader>
            <NavBar />
            {
              features.includes('new-nav-feature')
              && <NewNavFeature />
            }
         </NavHeader>
        <div className="content">
          <WrappedComponent features={features} {...props} />
        </div>
        { showFooter && <Footer /> }
      </>
    );
  };
};
```

但不能只咖喱布局功能。我们还需要满足`withProviders`的功能:

```
const withProviders = (options) =>
  compose(
    withUser,
    withFeatures,
    withLogger,
    withLayout(options)
  );
```

现在我们可以使用`withProviders`用我们需要的所有提供者包装任何页面组件，并为每个页面定制布局。

```
const MyPage = ({ user = {}, signIn, features = [], log }) => {
  return <>{/* our actual page content... */}</>;
};const MyPageWithProviders = withProviders({
  showFooter: false
})(MyPage);
```

# API 路由中的函数组合

函数组合不仅仅在客户端有用。它还可以用于处理 API 路线中的横切关注点。一些常见的问题包括:

*   证明
*   批准
*   确认
*   记录
*   错误处理

像上面的例子一样，我们可以使用函数组合来包装我们的 API 路由处理程序和我们需要的所有提供者。

下一个。JS 对 API 路由使用轻量级云函数，不再使用 Express。Express 主要对它的中间件栈和`app.use()`函数有用，它允许我们轻松地将中间件添加到我们的 API 路由中。

`app.use()`函数只是 API 中间件的异步函数组合。它是这样工作的:

```
app.use((request, response, next) => {
  // do something
  next();
});
```

但是我们可以用`asyncPipe`做同样的事情——一个可以用来组合返回承诺的函数的函数。

```
const asyncPipe = (...fns) => (x) =>
  fns.reduce(async (y, f) => f(await y), x);
```

现在，我们可以像这样编写我们的中间件和 API 路线:

```
const withAuth = async ({request, response}) => {
  // do something
};
```

在我们开发的应用中，我们有为自己创建服务器路径的功能。它基本上是一个围绕 asyncPipe 的薄薄的包装器，内置了一些错误处理功能:

```
const createRoute = (...middleware) =>
  async (request, response) => {
    try {
      await asyncPipe(...middleware)({
        request,
        response,
      });
    } catch (e) {
      const requestId = response.locals.requestId;
      const { url, method, headers } = request;
      console.log({
        time: new Date().toISOString(),
        body: JSON.stringify(request.body),
        query: JSON.stringify(request.query),
        method,
        headers: JSON.stringify(headers),
        error: true,
        url,
        message: e.message,
        stack: e.stack,
        requestId,
      });
      response.status(500);
      response.json({
        error: 'Internal Server Error',
        requestId,
      });
    }
  };
```

在您的 API 路由中，您可以像这样导入和使用它:

```
import createRoute from 'lib/createRoute';
// A pre-composed pipeline of default middleware
import defaultMiddleware from 'lib/defaultMiddleware';

const helloWorld = async ({ request, response }) => {
  request.status(200);
  request.json({ message: 'Hello World' });
};

export default createRoute(
  defaultMiddleware,
  helloWorld
);
```

有了这些模式，功能组合就形成了将应用程序中所有横切关注点集合在一起的主干。

任何时候你发现自己在想，“对于每一个组件/页面/路线，我需要做 X，Y，Z”，你就应该考虑用函数合成来解决问题。

# 后续步骤

[作曲软件](/javascript-scene/composing-software-the-book-f31c77fc3ddc)是一本畅销书，更深入地涵盖了作曲主题。

你知道吗，与大学学位相比，师徒关系更能带来高薪？我们创建了 [DevAnywhere.io](https://devanywhere.io/) 来为各个层次的软件构建者提供指导。从初级到 CTO，无论你在旅程的哪个阶段，我们都有一位导师可以帮助你更上一层楼。这可能是你职业生涯中最好的投资。

***埃里克·艾略特*** *是一位科技产品和平台顾问，《 [*【作曲软件】*](https://leanpub.com/composingsoftware)*[*【EricElliottJS.com】*](https://ericelliottjs.com/)*[*devanywhere . io*](https://devanywhere.io/)*的联合创始人，以及 dev 团队导师。他曾为 Adobe Systems、* ***、Zumba Fitness、*** ***【华尔街日报、*******【ESPN、*******【BBC】****等顶级录音艺人和包括* ***Usher、【Metallica】********

*他和世界上最美丽的女人享受着与世隔绝的生活方式。*