# :关注可访问性

> 原文：<https://medium.com/walmartglobaltech/focus-on-accessibility-41033dd9f82c?source=collection_archive---------7----------------------->

![](img/a1a5db4a0e294b8f4f51023870d82fa9.png)

Photo by [Stanley Dai](https://unsplash.com/@stanleydai?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

关于可访问性，浏览网页有很多方法。作为一名构建电子商务网页的软件工程师，我们努力做到兼容并蓄，让所有用户都能访问我们的页面。作为我们 ADA 用户群支持的一部分，我们利用各种工具和资源来支持关于我们应该如何在我们的应用中实现功能的决策。

对于高级用户来说，将手从键盘上拿开可能会影响工作效率。或者，对于行动不便的用户，键盘是他们浏览网页的唯一选择。

用户如何知道他们在网页上的位置？每个浏览器都呈现可以聚焦的 HTML 元素，并且每个浏览器都有自己的聚焦样式。可聚焦元素的例子有:按钮、表单元素、链接；然而，并不是所有的元素都能获得焦点，比如:div、p 或 img。

在 Internet Explorer 中，焦点样式是虚线矩形，类似于 Firefox，但在 Chrome 中，样式是蓝色阴影轮廓，类似地，在 Safari 中，样式是较浅的蓝色阴影轮廓。

浏览器的默认焦点样式可能不是最吸引人的，通常会被如下的自定义样式覆盖。

```
a:focus,
button:focus,
input:focus {
  outline: 1px solid grey;
}
```

该覆盖将在聚焦时在锚、按钮和输入元素周围设置一个实心灰色轮廓。尽管这是跨浏览器对齐焦点样式的快速解决方案，但是您忽略了可见性问题。

为了支持这种情况，考虑[网页内容可访问性指南(WCAG)](https://www.w3.org/WAI/standards-guidelines/wcag/) 。有两个规格将有助于确定使用默认或定制的焦点样式。

*第一，1.4.11*

[](https://www.w3.org/WAI/WCAG21/Understanding/non-text-contrast.html) [## 理解成功标准 1.4.11:非文本对比

### 成功标准 1.4.11 非文字对比(AA 级):以下视觉呈现有一个对比度…

www.w3.org](https://www.w3.org/WAI/WCAG21/Understanding/non-text-contrast.html) 

当一个元素的焦点样式被覆盖时，你将不得不考虑该元素及其周围元素的对比度。如果对比度不够明显，则当元素被聚焦时，聚焦样式不够明显。

*第二，2.4.7*

 [## 了解成功标准 2.4.7 |了解 WCAG 2.0

### 2.4.7 焦点可见:任何键盘可操作的用户界面都有一种操作模式，其中键盘焦点指示器是…

www.w3.org](https://www.w3.org/TR/UNDERSTANDING-WCAG20/navigation-mechanisms-focus-visible.html) 

在焦点轮廓被覆盖为“轮廓:无”的用例中；用户不再能看到焦点样式。如果元素使用自定义样式聚焦，不能保证一种聚焦样式将应用于页面上的所有元素。您可能需要覆盖网页不同部分元素的焦点样式，而不是一种样式适用于所有元素。

**结论**

基于规范 1.4.11 和 2 . 4 . 7；我们选择在所有页面上使用默认的焦点样式。我们可以依靠浏览器的默认设置在可聚焦的元素上应用聚焦样式，并减少 CSS 覆盖。你定制你的聚焦框样式吗？

您有兴趣了解更多关于沃尔玛 ADA 的信息吗？检查:

[https://medium . com/walmartlabs/be-accessible-always-5a da 07 AC 048](/walmartlabs/being-accessible-always-5ada0a7ac048)