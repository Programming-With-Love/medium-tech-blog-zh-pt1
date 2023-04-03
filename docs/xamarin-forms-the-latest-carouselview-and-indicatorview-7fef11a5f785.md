# x 主窗体:最新的旋转体视图和指示器视图

> 原文：<https://medium.com/globant/xamarin-forms-the-latest-carouselview-and-indicatorview-7fef11a5f785?source=collection_archive---------1----------------------->

![](img/cec2a77c593b07a8b168c104fc768edc.png)

在这篇博客中，我将向你解释如何在 Xamarin 表单中使用新的 carousel 视图和指示器视图。

## **旋转木马视图**

carousel 视图是一种以可滚动布局显示数据的视图，用户可以在其中滑动以浏览一组项目。默认情况下，CarouselView 水平显示其项目。

首先，单个项目将显示在屏幕上。通过滑动手势，我们可以在项目集合中前后导航。

## **如何填充 CarouselView？**

通过将 carousel 视图的 **ItemSource** 属性设置为任何实现 IEnumerable 的集合，可以用数据填充该视图。每个项目的外观可以在数据模板的 **ItemTemplate** 属性中定义。

## **指示器视图**

使用 IndicatorView，您可以轻松地向用户显示他们正在查看传送带视图的哪个部分。

**注意**:在写这个博客的时候(使用 Xamarin forms 4.6.0)我们必须设置**表单。调用表单前设置 flags(" indicator view _ Experimental ")**。Android 的 MainActivity.cs 中的 Init()和 iOS 的 AppDelegate.cs。如果不这样做，您将会得到一个未处理的托管异常。

> 在 Xamarin Forms 4.5.0.356 之前，我们可以在 IndicatorView 中使用带有 **ItemsSource** 属性的 IndicatorView。现在他们已经移除了 ItemSource 方法，并用 CarouselView 中的 IndicatorView 属性替换了它。

**在 XAML**

```
<?xml version="1.0" encoding="UTF-8" ?>
<ContentPage
    x:Class="XamarinTest.CarouselViewExample"
    ae lh" href="http://xamarin.com/schemas/2014/forms" rel="noopener ugc nofollow" target="_blank">http://xamarin.com/schemas/2014/forms"
    xmlns:x="[http://schemas.microsoft.com/winfx/2009/xaml](http://schemas.microsoft.com/winfx/2009/xaml)">
    <AbsoluteLayout>
        <CarouselView
            x:Name="TheCarousel"
            AbsoluteLayout.LayoutBounds="0,0,1,1"
            AbsoluteLayout.LayoutFlags="All"
            **IndicatorView="indicatorview"**>
            <CarouselView.ItemTemplate>
                <DataTemplate>
                    <AbsoluteLayout>
                        <StackLayout
                            AbsoluteLayout.LayoutBounds=
                            "0.5,0.3,AutoSize,AutoSize"
                            AbsoluteLayout.LayoutFlags=
                            "PositionProportional"
                            HorizontalOptions="Center">
                            <Label
                                FontSize="Title"
                                HorizontalOptions="Center"
                                HorizontalTextAlignment="Center"
                                Text="{Binding .}"
                                VerticalOptions="Center" />
                        </StackLayout>
                    </AbsoluteLayout>
                </DataTemplate>
            </CarouselView.ItemTemplate>
        </CarouselView> <IndicatorView
            x:Name="indicatorview"
            AbsoluteLayout.LayoutBounds="0.5,0.95,100,100"
            AbsoluteLayout.LayoutFlags="PositionProportional"
            IndicatorColor="LightGray"
            IndicatorSize="10"
            SelectedIndicatorColor="Black" />
    </AbsoluteLayout>
</ContentPage>
```

**在代码隐藏中**

```
public partial class CarouselViewExample : ContentPage
    {
        public CarouselViewExample()
        {
            InitializeComponent();
            var list = new List<string>
            {
                "Hey",
                "Did you check the",
                "The CarouselView",
                "In Xamarin.Forms?"
            };
            TheCarousel.ItemsSource = list;
        }
    }
```

这段代码是用 Xamarin forms 4.6.0.847 编写的

## 输出

![](img/9fbe7a60b21551cabba2121dfa0ea611.png)