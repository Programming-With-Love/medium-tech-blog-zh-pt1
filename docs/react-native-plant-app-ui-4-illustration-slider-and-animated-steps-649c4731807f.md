# React 原生植物应用程序 UI #4:插图滑块和动画步骤

> 原文：<https://medium.com/quick-code/react-native-plant-app-ui-4-illustration-slider-and-animated-steps-649c4731807f?source=collection_archive---------0----------------------->

![](img/1f37c407fde4b134966efd211fb33cf8.png)

本教程是我们 React 原生植物应用教程系列的第四部分。在[的前一部分](https://kriss.io/react-native-plant-app-ui-3-implementing-welcome-screen/)中，我们成功地实现并截取了欢迎屏幕的一些 UI 部分。本教程是我们上一部分停止的同一教程的继续。因此，建议仔细阅读前一部分，以便对整个项目有所了解和认识。

如果您想从头开始学习，可以在下面找到本教程系列之前的所有部分:

*   [React 原生植物 App UI #1:入门](https://kriss.io/react-native-plant-app-ui-1-getting-started/)
*   [React 本地工厂应用 UI #2:实现定制组件](https://kriss.io/react-native-plant-app-ui-2-implementing-custom-components/)
*   [构建 React 原生植物应用 UI #3:实现欢迎屏幕](https://kriss.io/react-native-plant-app-ui-3-implementing-welcome-screen/)

如前所述，撰写本系列教程的动机来自于 [React Native 应用程序模板](https://www.instamobile.io/)，这些模板提供了各种用 React Native 编写的移动应用程序模板，并由通用特性和设计提供支持。这些应用程序模板允许我们实现自己的应用程序，甚至启动自己的创业公司。此外，第四部分也是 Youtube 视频教程中的编码实现和设计的延续，由 [React UI Kit](https://www.youtube.com/watch?v=gyiwFcrVRCM&list=PLNRPou200YIeu4UllJkv8-Ca19Ld_eOay&index=2) 用于 Plant 应用程序。视频教程似乎非常彻底地交付了整体 app 的编码实现。但是，没有对编码和实现的口头指导。本教程系列是实现相同的编码风格和设计形式的文章。因此，学习者可以经历每一步，慢慢理解实现。

## 概观

在这个系列教程的第四部分中，我们将实现我们在上一部分教程中分离出来的 UI 部分。我们分离的 UI 部分是插图部分和步骤(分隔符点)部分。这个想法是从在欢迎屏幕上用水平图像滑块实现插图部分开始。然后，我们将添加分隔符点称为步骤部分下面的图像插图。然后，我们将根据插图图像的滑动为活动分隔符点设置动画。

那么，让我们开始吧！！

## 将图像作为道具导入

这里，我们将为插图滑块部分导入图像。我们将使用`defaultProps`模块导入插图作为道具。为此，我们需要使用以下代码片段中的代码:

```
Welcome.defaultProps = {
  illustrations: [
    { id: 1, source: require('../assets/images/illustration_1.png') },
    { id: 2, source: require('../assets/images/illustration_2.png') },
    { id: 3, source: require('../assets/images/illustration_3.png') },
  ],
};
```

在这里，我们定义了一个名为`illustrations`的数组，它保存图像为`id`和`source`的对象。现在，我们可以使用插图变量作为欢迎屏幕中的道具。

## 实施插图部分

现在，我们要以函数`renderIllustrations()`的形式实现我们在之前教程中截取的插图部分。在这个插图部分，我们将把图像显示为一个水平滑块。为此，我们需要使用以下代码片段中的代码:

```
state = {
  }

renderIllustrations(){
    const { illustrations } = this.props;
```

这里，我们已经初始化了`state`变量。此外，我们已经从 props 中定义了`illustrations`常量。现在，我们将插图部分中的图像显示为水平滑块。

## 使用`FlatList`组件

这里，我们将使用`FlatList`组件来渲染图像。然后，我们将使用不同的道具配置来配置`FlatList`，以便正确显示水平图像滑块。为此，我们需要在`renderIllustrations()`函数中使用以下代码片段中的代码:

```
<FlatList
  horizontal
  pagingEnabled
  scrollEnabled
  showsHorizontalScrollIndicator={false}
  scrollEventThrottle={16}
  snapToAlignment="center"
  data={illustrations}
  extraDate={this.state}
  keyExtractor={(item, index) => `${item.id}`}
  renderItem={({ item }) => (
    <Image
      source={item.source}
      resizeMode="contain"
    />
  )}
/>
```

这里，我们有一个`FlatList`，它具有不同的螺旋桨配置，解释如下:

*   `pagingEnabled`:当其值为 true 时，滚动时滚动视图停止在滚动视图大小的倍数上。默认值为 false。
*   `scrollEnabled`:当其值为 false 时，不能通过触摸交互滚动视图。默认值为 true。
*   `showsHorizontalScrollIndicator`:当其值为 false 时，底部的水平滚动条不显示。
*   `scrollEventThrottle`:该属性用于控制滚动时触发滚动事件的频率(以毫秒为单位的时间间隔)。数字越小，跟踪滚动位置的代码越准确。
*   `snapToAlignment`:该属性将定义捕捉与滚动视图的关系。

这里，我们还有`keyExtractor`道具，用于惟一地标识列表中的每一项。然后，我们有了`renderItem` prop，它使我们能够返回列表中每一项的模板。在这种情况下，它返回来源于`illustrations`数组的`Image`组件。

因此，我们将在模拟器屏幕中获得以下结果:

![](img/5395be6fbcdeece2badb67f83418abb5.png)

正如我们所看到的，我们得到了带有水平图像滑块的插图部分。但是，图像和滑动动作似乎并不吸引人。因此，我们将对我们的`FlatList`和它的`Image`组件使用一些样式属性。

## **配置插图样式**

这里，我们将为平面列表及其图像组件配置一些样式。为此，我们将使用 react-native 包中的`Dimensions`组件。这个`Dimensions`组件允许我们获得应用程序屏幕的完整高度和宽度，这样我们就可以使用这些尺寸变量来配置样式。为此，我们需要首先导入`Dimensions`组件，如下面的代码片段所示:

```
import { StyleSheet, FlatList, Image, Dimensions } from 'react-native';
```

现在，我们需要使用由`Dimensions`组件提供的`get()`函数，如下面的代码片段所示:

```
const { width, height } = Dimensions.get('window');
```

现在，我们将使用这些`width`和`height`常量来设计`renderIllustrations()`函数中的插图:

```
<FlatList
   horizontal
   pagingEnabled
   scrollEnabled
   showsHorizontalScrollIndicator={false}
   scrollEventThrottle={16}
   snapToAlignment="center"
   data={illustrations}
   extraDate={this.state}
   keyExtractor={(item, index) => `${item.id}`}
   renderItem={({ item }) => (
     <Image
       source={item.source}
       resizeMode="contain"
       style={{ width, height: height / 2, overflow: 'visible' }}
     />
   )}
 />
```

因此，我们将在模拟器屏幕中获得以下结果:

![](img/30bca464d3d1040bb56264dfaf2c2058.png)

正如我们所看到的，我们现在已经有了插图部分和一个合适的图像滑块。而且，滑动动作看起来很流畅，很吸引人。

## 实施步骤部分

在这一步中，我们将实现步骤部分，它将根据插图部分图像的数量包括分隔符点。因为有三个图像，所以将有三个定界符点。在上一篇教程中，我们已经将这一部分分割为`renderSteps()`函数。现在，我们将向该函数添加分隔符点。为此，我们需要使用以下代码片段中的代码:

```
renderSteps(){
   const { illustrations } = this.props
   return(
     <Block row center middle style={styles.stepsContainer}>
       {illustrations.map((item, index) => {
         return (
           <Block
             animated
             flex={false}
             key={`step-${index}`}
             color="gray"
             style={[styles.steps]}
           />
         )
       })}
     </Block>
   )
 }
```

这里，在`renderSteps()`函数中，我们从 props 中定义了`illustrations`常量，就像在`renderIllustration()`函数中一样。然后，我们返回了一个父组件为`Block`的模板和一些样式道具。块模板包裹定界符点的模板。这里为了使定界符点的数量等于插图图像的数量，我们使用了插图数组中的`map()`数组函数。`map()`函数遍历插图数组中的每一项，并返回每一项的模板。在这种情况下，我们使用`map()`功能只是为了使定界符点的数量等于插图图像的数量。然后，在 map()函数内部，我们已经返回了带有道具和样式的`Block`组件。

下面的代码片段提供了所需的样式:

```
const styles = StyleSheet.create({
  stepsContainer: {
    position: 'absolute',
    bottom: theme.sizes.base * 3,
    right: 0,
    left: 0,
  },
  steps: {
    width: 5,
    height: 5,
    borderRadius: 5,
    marginHorizontal: 2.5,
  },
});
```

因此，我们将在模拟器屏幕中获得以下结果:

![](img/b5b356a516f84649e0ebf1a15cf05dc3.png)

正如我们所看到的，我们在插图图像的底部有分隔符点。到目前为止，这些点将只是看起来毫无生气的风格。但是我们将在图像滑动的基础上，为这些点添加带有动画的活动样式。

## **添加活动风格动画**

在这里，我们将添加动画到我们的步骤部分的分隔符点。为此，我们需要从 react-native 包中导入`Animated`组件，如下面的代码片段所示:

```
import { StyleSheet, FlatList, Image, Dimensions, Animated } from 'react-native';
```

现在，我们需要定义一个名为`scrollX`的变量，它被初始化为`Animated`值。这个变量将为水平动画存储我们的动画值。为此，我们需要使用以下代码片段中的代码:

```
export default class Welcome extends React.Component {

  scrollX = new Animated.Value(0);

  state = {
  }

.......................
```

这里，`Animated.Value`配置使我们能够绑定到样式属性或其他属性，也可以进行插值。

现在，我们需要将这个`scrollX`值配置到`renderIllustrations()`函数的`FlatList`组件的`onScroll`事件中，如下面的代码片段所示:

```
<FlatList
   horizontal
   pagingEnabled
   scrollEnabled
   showsHorizontalScrollIndicator={false}
   scrollEventThrottle={16}
   snapToAlignment="center"
   data={illustrations}
   extraDate={this.state}
   keyExtractor={(item, index) => `${item.id}`}
   renderItem={({ item }) => (
     <Image
       source={item.source}
       resizeMode="contain"
       style={{ width, height: height / 2, overflow: 'visible' }}
     />
   )}
   onScroll={
     Animated.event([{
       nativeEvent: { contentOffset: { x: this.scrollX } }
     }])
   }
 />
```

这里，我们使用了`Animated`组件的`event`函数，它将`nativeEvent`作为一个参数。然后，我们根据`nativeEvent`配置中的`scrollX`变量定义了`contentOffset`值。

## **根据滑动图像在定界符点中配置动画**

这里，我们将为定界符点添加动画，这将取决于`renderIllustrations()`函数的`FlatList`组件的`onScroll`事件。为此，我们需要首先使用`Animated`组件的`divide`函数初始化步进位置。`divide`函数创建一个新的`Animated`值，该值由第一个动画值除以第二个动画值组成。下面的代码片段显示了它的用法:

```
const stepPosition = Animated.divide(this.scrollX, width);
```

现在，我们将在`renderSteps()`函数中进行一些配置，以便为定界符点添加动画属性。为此，我们需要在`renderSteps()`函数中使用下面代码片段中的代码:

```
<Block row center middle style={styles.stepsContainer}>
   {illustrations.map((item, index) => {
     const opacity = stepPosition.interpolate({
       inputRange: [index - 1, index, index + 1],
       outputRange: [0.4, 1, 0.4],
       extrapolate: 'clamp',
     });

     return (
       <Block
         animated
         flex={false}
         key={`step-${index}`}
         color="gray"
         style={[styles.steps, { opacity }]}
       />
     )
   })}
 </Block>
```

这里，我们定义了一个名为`opacity`的常量，它被初始化为`stepPosition`常量`initlized`到`Animated.divide`的`interpolate()`函数。`interpolate()`函数以`inputRange`、`outputRange`和`extrapolate`为参数值。`interpolate()`功能允许输入范围映射到不同的输出范围。然后，我们将`opacity`常量添加到`Block`组件的样式属性中。

因此，我们将在模拟器屏幕中获得以下结果:

![](img/7064d67e96ce87234f47b3d377443fae.png)

正如我们所看到的，当滑动插图部分的图像时，我们在步骤部分的分隔符点中获得了活动样式动画。最重要的是，动画看起来非常流畅和吸引人。至此，我们已经结束了这部分的教程。

最后，我们在 Plant 应用程序 UI 的欢迎屏幕中成功实现了带有图像滑块的插图部分和带有动画分隔符点的步骤部分。

## **结论**

本教程是 React Native Plant App UI 教程系列的第四部分。在这一部分中，我们从本系列教程的第三部分中停止的地方继续。在教程的这一部分，我们得到了如何使用`FlatList`组件将图像滑块添加到插图部分的逐步指导。我们还学习了如何向步骤部分的分隔符点添加漂亮的活动样式动画。因此，我们根据插图部分中图像的滑动制作了测光点的活动动画。

在本系列教程的下一部分中，我们将使用欢迎屏幕上的模态视图来实现服务条款部分。