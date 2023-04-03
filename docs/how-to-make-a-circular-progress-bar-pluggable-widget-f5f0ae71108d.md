# 如何制作一个循环进度条插件

> 原文：<https://medium.com/mendix/how-to-make-a-circular-progress-bar-pluggable-widget-f5f0ae71108d?source=collection_archive---------2----------------------->

![](img/4af865eb49d55a4b8c2cfe8d7b70f831.png)

# 在我的日常生活中，我并不经常有机会创建可插拔的小部件，所以为了保持我的技能新鲜，我喜欢通过尝试重新创建一个我经常使用的小部件来挑战自己。我已经有一段时间没有这样做了，所以本周我决定测试一下我的技能，尝试重新创建圆形进度条小部件，它在原生移动资源模块中提供。因此，请系好安全带，让我们在 React 和 Mendix 的世界中开始一次探索之旅。

# 开始之前

请在继续之前花一点时间，确保在我们开始之前您已经做好了一切准备。您将需要:

*   Mendix Studio Pro(我在这个例子中用的是(9.1.1)。
*   在您的机器上安装并配置了节点 js。
*   你选择的 IDE，我用了 VS 代码。
*   建议您完成文档中关于可插拔部件的教程，或者完成 T2 学院的课程。
*   安装在测试设备上的 [Make it Native 9](https://docs.mendix.com/refguide/getting-the-make-it-native-app) 应用程序

# 选择要使用的 github 库

对于开发人员来说，知道从哪里开始这样的项目有时会令人望而生畏，这就是为什么我发现在一些研究之后知道要做什么会容易得多。所以我去了 github，看看那里已经有什么了。我从 [**epicode-academy**](https://github.com/msadura/epicode-academy) 看到了这个回购，并决定将它作为我的组件的基础。在这个回购中还有一些其他项目，所以如果你正在寻找确切的联系，这里是。

# 分析代码

在开始编码之前，重要的是实际检查所提供的代码，并确保您理解发生了什么。看一下这个例子，我已经看到它使用了功能组件，我知道这需要一些修改才能在一个可插拔的小部件中实现，因为主组件通常是一个类组件。

> 组件有两种类型，类组件和函数组件。功能组件只是一个普通的 JavaScript 函数，它接受 props 作为参数并返回一个 React 元素。一个类组件需要你从 React 扩展。

# 创建小部件支架

看够了，我们来编码吧！首先，打开您的终端并导航到您的项目目录。一个快速的方法是点击 Studio Pro 顶部菜单中“应用程序”下的“在浏览器中显示应用程序目录”。这将打开一个文件浏览器，其中包含您的应用程序文件。复制导航栏中的文件路径，然后返回到您打开的终端。键入“cd”并粘贴到您的文件路径中，如下所示:

`cd yourFilePath`

接下来，我们必须创建一个文件夹来存储定制的小部件。在您的终端中键入:

`mkdir CustomWidgets`

然后使用 cd 进入刚刚创建的文件夹:

`cd CustomWidgets`

现在，您可以使用小部件构建器来创建小部件支架。再次在终端中，使用该命令调用 Mendix 小部件生成器:

`@mendix/widget CircularProgressBar`

然后，小部件生成器将通过询问一些问题来指导您创建小部件，以下是我使用的方法:

*   小工具名称:*{您的小工具名称}*
*   小工具描述:*{您的小工具描述}*
*   组织名称:*{您的组织名称}*
*   版权:*{您的版权日期}*
*   许可证:*{您的许可证}*
*   初始版本:*{您的初始版本号}*
*   作者:*{您的作者姓名}*
*   Mendix 项目路径:*../../*
*   编程语言: **Javascript ES6**
*   Widget 类型:**原生手机**
*   Widget 模板:**空 widget(推荐有经验的开发者使用)**
*   单元测试:**否**
*   端到端测试:**否**

# 设置小部件 XML

我们需要做的第一件事是改变小部件 XML，以便我们能够以属性的形式从上下文实体接收数据。为此，我们需要创建一个可以接受整数值的属性，为该属性定义键值也很重要。你可以在下面看到我的小部件 XML。

```
<?xml version="1.0" encoding="utf-8"?><widget id="mendix.circularprogressbar.CircularProgressBar" needsEntityContext="true" offlineCapable="true" pluginWidget="true"supportedPlatform="Native" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"xsi:schemaLocation="http://www.mendix.com/widget/1.0/ ../node_modules/mendix/custom_widget.xsd"><name>Circular Progress Bar</name><description>Animated Circle Progress widget</description><icon>iVBORw0KGgoAAAANSUhEUgAAAEAAAABACAMAAACdt4HsAAABp1BMVEUAAABV//9mzP9LtP9Ms/9Jtv9NsvdJsfpLtPpJsfdJsfhJsvhJsvdKsvdJsPhKsPhJsfdJsPhJsfdIsfhJsfdIsPdJsfhJsfhJsPhJsPhIsfhIsPdJsPdKsPdKsfdNsvdOsvdPs/dQs/dRtPdStPdTtPdUtfdWtvdXtvdauPdcuPdeufdeufhguvhiu/hju/hkvPhmvfhnvfhpvvhrv/huwPhvwfhxwfhywvhzwvh4xfl5xfl6xfl8xvl9xvl9x/mByPmCyfmFyvmGyvmJzPmKzPmLzfmNzvqPzvqQz/qT0PqU0PqU0fqX0vqY0vqa0/qe1fqg1vqj1/uk1/un2fup2vut2/uv3Puw3Puw3fuz3vu13/u23/u34Pu44Pu64fu64fy84vy94vy+4/y/4/zD5fzE5fzG5vzH5vzI5/zK6PzL6PzR6/zT7P3U7P3V7f3W7f3Y7v3Z7v3c8P3e8f3f8f3g8f3i8v3l8/3l9P3n9P3r9v7t9/7u9/7v+P7w+P7x+f7y+f70+v71+v74/P75/P76/f77/f78/f78/v79/v7+/v7////6dMsRAAAAG3RSTlMAAwURGxwhMTNic3SEh4iVp7XBzejt7vH5/f6PsMNWAAABsklEQVR4AWIYfGAUjIJRMAqYuYREJKWJAqLCPGwY+jnFpEkBEryMqPr5pEkFgkwo9kuTDviR/S9GhgFSHAgDuKXJAQIIA4TIMkAcEY4i0mQBVrgBkuQZwA43QJo8wIFhQEhEOIBQOutHJozDOP5Crp4e1RhkJ0tKGJFd6oNEdtmJyEIzpaZl5nrRZgaHM/2Pf5/vwXXfyagXgG93bwSAlEolowLMm9w83gibhXH2gKKVdD67gTnWjwCk+VVjMQS4suSnnjMLRVFc9sAHvAX2A9fySaXNBMbEZVUWscaHIMRuqwBgD8hDEbnsRmfjUKJkAQZGCTlO/xWBwIADQLIZBlY441MvfoF1xlFS/4fy+bzXKh4dgNJE7L3eh3tmtuWa+AMcMIY3dgUvZQpGEYmMw2kD7HC+R29UqyoXLaBd0QZxzgXgikLLDSqJTKU5HOcS0MsbA9jPqtwCRvXm2eorBbNIJBw3KJ9O4Yl+AAXdnyaLt7PWN3jRWLvzmAVp94zO5+n41/onfo/UpExxZqI0O7NQr0DhIq9Io7hQpbRYp7hiobRqo6ByFcNWuY6CUTAKRgEAo8X0lBD3V30AAAAASUVORK5CYII=</icon><properties><propertyGroup caption="General"><property key="progress" type="attribute" required="true"><caption>Progress Indicator</caption><description>The attribute that contains the circularprogressbar value, should be an integer between 0 and 100</description><attributeTypes><attributeType name="Integer"/></attributeTypes></property></propertyGroup></properties></widget>
```

# 主要成分

既然我们已经定义了数据输入，我们就可以处理主要组件了。在这种情况下，主组件只是用来呈现子组件，并向它传递使用其 props 提供的数据。

> “Props”是 React 中的一个特殊关键字，代表属性，用于将数据从一个组件传递到另一个组件。但是这里重要的部分是数据属性只在单向流中传递。

这里需要注意的是，如前所述，主组件是作为类组件在脚手架中创建的。这意味着我们需要稍微修改 Github 代码。

这是我的主要组件 CircularProgressBar 的代码。

```
import { React,Component ,createElement} from "react";import CircularProgress from "./components/CircleComponent";export class CircularProgressBar extends Component {constructor(props){super(props);this.handleChange =this.handleChange.bind(this);const {progress} = this.props;console.log('constuctor triggered');}render() {const {progress} = this.props;console.log('render triggered');return (<CircularProgressprogress={progress.value}size={200}/>)}}
```

# 子组件

子组件是我们大部分逻辑发生的地方。这里我们再次使用 props 接受来自父组件的数据，一点逻辑和来自“styled-components/native”的样式库来样式化组成进度条的各个部分。最后，我们以一个应该呈现组件的 return 语句结束。检查下面的子组件“CircleComponent”代码。

```
import React, { useRef, useEffect, createElement } from "react";import styled from 'styled-components/native';import {Animated} from 'react-native';const EmptyColour = '#a0a0a1';const ProgressColour = '#0085ff';const CircleBase = styled(Animated.View)`width: ${props => props.size}px;height: ${props => props.size}px;border-radius: ${props => props.size / 2}px;border-width: ${props => props.size / 10}px;`;const EmptyCircle = styled(CircleBase)`border-color: ${EmptyColour};justify-content:center;align-items: center;transform: rotate(-45deg);`;const Indicator = styled(CircleBase)`position: absolute;border-left-color:${ProgressColour};border-top-color:${ProgressColour};border-bottom-color:transparent;border-right-color:transparent;`;const CoverIndicator = styled(CircleBase)`position: absolute;border-left-color:${EmptyColour};border-top-color:${EmptyColour};border-bottom-color:transparent;border-right-color:transparent;`;export default function CircularProgress(props) { //added input propsconst {progress, size} = this.props //destructured the propsconsole.log (styled)const animatedProgress = useRef(new Animated.Value(0)).current;const animateProgress = useRef(toValue => {Animated.spring(animatedProgress, {toValue,useNativeDriver: true,}).start();}).current;useEffect(() => {animateProgress(progress);}, [animateProgress,progress]);const firstIndicatorRotate = animatedProgress.interpolate({inputRange: [0, 50],outputRange: ['0deg', '180deg'],extrapolate: 'clamp',});const secondIndicatorRotate = animatedProgress.interpolate({inputRange: [0, 100],outputRange: ['0deg', '360deg'],extrapolate: 'clamp',});const secondIndictorVisibility = animatedProgress.interpolate({inputRange: [0, 49, 50, 100],outputRange: [0, 0, 1, 1],extrapolate: 'clamp',});return (<EmptyCircle size={size}><Indicatorstyle={{transform: [{rotate: firstIndicatorRotate}]}}size={size}/><CoverIndicator size={size} /><Indicatorsize={size}style={{transform: [{rotate: secondIndicatorRotate}],opacity: secondIndictorVisibility,}}/></EmptyCircle>);}
```

# 安装依赖项

差不多到了测试您的小部件的时候了，但是在此之前我们还有一些事情要处理。我们必须确保我们使用的任何库都被正确地导入到我们的 widget 文件夹中。再次打开您的终端并键入命令。

`npm install --save`

等待它下载并安装您的代码可能需要的任何依赖项。

如果出现任何问题，或者您需要重新安装，您也可以执行全新安装，删除所有节点模块，并使用这个命令**重新安装它们(但是只有在 100%需要时才这样做！)**

`npm ci --save`

# 创建小部件。mpk 文件。

捆绑您的小部件并创建一个。mpk 可以在您的 Mendix 项目中使用，在您的终端中运行以下命令:

`npm run build`

此操作将构建您的小部件代码，然后将小部件复制到您的小部件文件夹中。最后一步是在 studio pro 中同步您的应用程序目录，方法是按 F4，或者转到 Studio Pro 顶部菜单中的“应用程序”→“同步应用程序目录”。

现在，您可以在 Studio Pro 中开发时访问您的小部件。该小部件需要放置在上下文的数据视图中，并期望一个整数属性连接到它，但一旦你设置好了，你就可以使用 Make it Native 9 应用程序运行并测试你的新小部件。

# 阅读更多

*   [https://docs . mendix . com/ref guide/get-the-make-it-native-app](https://docs.mendix.com/refguide/getting-the-make-it-native-app)
*   [https://docs . mendix . com/how to/extensibility/create-a-pluggable-widget-one](https://docs.mendix.com/howto/extensibility/create-a-pluggable-widget-one)
*   [https://docs . mendix . com/how to/extensibility/create-a-pluggable-widget-two](https://docs.mendix.com/howto/extensibility/create-a-pluggable-widget-two)
*   [https://academy . mendix . com/link/path/108/Build-a-Pluggable-Widget](https://academy.mendix.com/link/path/108/Build-a-Pluggable-Widget)
*   [https://docs . mendix . com/API docs-mxsdk/API docs/pluggable-widgets](https://docs.mendix.com/apidocs-mxsdk/apidocs/pluggable-widgets)
*   [https://docs . mendix . com/API docs-mxsdk/API docs/client-API-for-pluggable-widgets](https://docs.mendix.com/apidocs-mxsdk/apidocs/client-apis-for-pluggable-widgets)

*来自发布者-*

*如果你喜欢这篇文章，你可以在我们的* [*媒体页面*](https://medium.com/mendix) *或我们自己的* [*社区博客网站*](https://developers.mendix.com/community-blog/) *找到更多类似的文章。*

*希望入门的创客，可以注册一个* [*免费账号*](https://signup.mendix.com/link/signup/?source=direct) *，通过我们的* [*学苑*](https://academy.mendix.com/link/home) *即时获取学习。*

有兴趣更多地参与我们的社区吗？你可以加入我们的 [*Slack 社区频道*](https://join.slack.com/t/mendixcommunity/shared_invite/zt-hwhwkcxu-~59ywyjqHlUHXmrw5heqpQ) *或者想更多参与的人，看看加入我们的* [*遇见 ups*](https://developers.mendix.com/meetups/#meetupsNearYou) *。*