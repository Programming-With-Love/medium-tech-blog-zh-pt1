# 迁移 Pinterest 档案以作出反应

> 原文：<https://medium.com/pinterest-engineering/migrating-pinterest-profiles-to-react-479f4f7306aa?source=collection_archive---------2----------------------->

Imad Elyafi | Pinterest 工程师，核心体验

自 2012 年以来，我们在[主干](http://http://backbonejs.org/)之上扩展了我们的网络框架丹泽尔(以有史以来最伟大的演员命名)。但如今，[的反应](https://facebook.github.io/react/)是金标准。它拥有一个庞大的开发人员社区，能够实现卓越的工程速度和性能。在这里，我们将看看我们尝试的技术和我们在迁移到 React 时面临的挑战，从 Pinner 配置文件开始。

![](img/9e1d786017b8d2b32991db60a525d230.png)

## 准备基础设施(服务器端 Nunjucks)

我们经常发布新功能，每天进行数百次实验，所以我们不能为了在 React 中重建整个网站而停止产品开发。虽然在 React 中构建一个新的 web 应用程序相对容易，但迁移一个不断变化并被数百万人使用的服务却是一个复杂得多的挑战。这就像在飞行中更换飞机引擎一样。

之前，我们在 Python 中使用了 [Jinja](http://jinja.pocoo.org/) 模板引擎进行服务器端渲染，并使用了名为 [Nunjucks](https://mozilla.github.io/nunjucks/) 的 JavaScript 等价物进行客户端浏览器渲染。这些模板引擎非常相似，允许我们进行通用渲染(在客户端和服务器端使用相同的模板)。因为 React 不能在 Python 中呈现，所以作为第一步，我们在独立的 NodeJS 服务器上启用了 Nunjucks 呈现。现在，我们有了纯粹的同构渲染，JavaScript 在服务器端和客户端。

## 丹泽尔反应桥

为了让工程师能够逐步将 UI 的核心部分转换为 React，我们在 Denzel 内部启用了 React 渲染。在大多数情况下，可以使用 React.render()，所以我们用一个新的关键字 component 向 Nunjucks 的模板语言添加了 React 特定的绑定，以表示 Denzel 和 React 之间的“桥梁”。结合我们的 [A/B 测试框架](https://engineering.pinterest.com/blog/building-pinterest%E2%80%99s-ab-testing-platform)，我们可以根据传统 Denzel 组件轻松测量 React 组件，并控制某些指标，如 Pinner 等待时间、首字节时间和错误率。

下面是一个在 Nunjucks 模板中呈现 MyReactComponent.js 的示例:

```
{% if in_react %}
  {{ component('MyReactComponent', {pinId: '123'}) }}
{% else %}
  {{ module('MyDenzelComponent', pinId='123') }}
{% endif %}
```

## 适配器的高阶组件

最后一步是向新创建的 React 组件提供数据。我们创建了一个名为 withResource 的高阶组件(HOC ),以便轻松地从我们的 API 获取数据，同时保持与其他 HOC 的可组合性。withResource 的简化版本为包装的组件提供了一个数据属性:

```
import ResourceFactory from 'ResourceFactory';

export default withResource = ({ name, options }) => (Subject) => {
  return class extends Component {

    state = {
      data: null
    };

    componentDidMount() {
      const resourceOptions = getOptions(options, this.props);
      const resource = ResourceFactory.create(name, resourceOptions);

      resource.callGet().then((resourceResponse) => {
        this.setState({ data: resourceResponse.data });
      });
    }

    render() {
      return (
        <Subject
          data={this.state.data}
          {...this.props}
        />
      );
    }
  };
};
```

## 例子

```
import withResource from 'withResource';

class MyComponent extends Component {
  render() {
    return (
      <div>{'Username:' + this.props.data.name}</div>
    );
  }
};

export default withResource({
  name: 'MyResource',
  options: props => {
    return { pin_id: props.pinId };
  },
})(MyComponent);
```

## 结果

在转换 Pinners 的档案以作出反应的过程中，我们看到了持续的绩效和参与度提高。绩效和参与度指标各增长了 20%。当我们转换配置文件时，web 产品工程师继续对旧的 Denzel 组件进行更改，同时创建新的 React 组件。

鸣谢:该项目的核心贡献者包括 Imad Elyafi、Braden Anderson、Chris Lloyd、Kevin Grandon、Jessica Chan 以及 WebCore Experience 团队的其他成员。公司的许多工程师也提供了有益的反馈。