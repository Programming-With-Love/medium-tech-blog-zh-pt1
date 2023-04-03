# Android 数据绑定:动画

> 原文：<https://medium.com/androiddevelopers/android-data-binding-animations-55f6b5956a64?source=collection_archive---------2----------------------->

![](img/b4dda3d4b006b4193950e3f793e40f3b.png)

## 当数据在一瞬间没有变化时

我已经展示了如何在数据改变时使用 Android 数据绑定来更新视图。这是查看 UI 即时更新的一个很好的方式，但是有时您想引起对数据变化的注意，或者只是提供一个平滑的过渡。使用数据绑定添加动画有两种方法:使用绑定适配器或使用 OnRebindCallback。

## 绑定适配器动画

绑定适配器是一种在数据绑定值更改时设置视图值的方法。为动画使用绑定适配器非常简单，但是如果您需要复习，可以看看[自定义设置器文章](/google-developers/android-data-binding-custom-setters-55a25a7aea47#.tlzme9784)。我们可以使用一个绑定适配器来截取值的变化，并使其动画化。

这是一个淡入或淡出视图的方法:

```
@BindingAdapter(**"animatedVisibility"**)
**public static void** setVisibility(**final** View view,
                                 **final int** visibility) {
    *// Were we animating before? If so, what was the visibility?* Integer endAnimVisibility =
            (Integer) view.getTag(R.id.***finalVisibility***);
    **int** oldVisibility = endAnimVisibility == **null** ? view.getVisibility()
            : endAnimVisibility;

    **if** (oldVisibility == visibility) {
        *// just let it finish any current animation.* **return**;
    }

    **boolean** isVisibile = oldVisibility == View.***VISIBLE***;
    **boolean** willBeVisible = visibility == View.***VISIBLE***;

    view.setVisibility(View.***VISIBLE***);
    **float** startAlpha = isVisibile ? 1f : 0f;
    **if** (endAnimVisibility != **null**) {
        startAlpha = view.getAlpha();
    }
    **float** endAlpha = willBeVisible ? 1f : 0f;

    *// Now create an animator* ObjectAnimator alpha = ObjectAnimator.*ofFloat*(view,
            View.***ALPHA***, startAlpha, endAlpha);
    alpha.setAutoCancel(**true**);

    alpha.addListener(**new** AnimatorListenerAdapter() {
        **private boolean isCanceled**;

        @Override
        **public void** onAnimationStart(Animator anim) {
            view.setTag(R.id.***finalVisibility***, visibility);
        }

        @Override
        **public void** onAnimationCancel(Animator anim) {
            **isCanceled** = **true**;
        }

        @Override
        **public void** onAnimationEnd(Animator anim) {
            view.setTag(R.id.***finalVisibility***, **null**);
            **if** (!**isCanceled**) {
                view.setAlpha(1f);
                view.setVisibility(visibility);
            }
        }
    });
    alpha.start();
}
```

你可以看到，当视图处于动画过程中时，我考虑了当前的 alpha。这是一点额外的工作，但它将有助于用户流畅的动画体验。为了更好，你还可以考虑持续时间中的 alpha，这样当淡入被淡出打断时，淡出会更短。由于这是一个简短的动画，它看起来并不太糟糕，即使被打断。

我选择使用自定义属性，这样只有我使用“app:androidvisibility”的视图才会淡入淡出。如果我为“android:visibility”创建了一个绑定适配器，那么所有在该属性上有绑定表达式的视图都会淡入淡出。

## OnRebindCallback

ViewDataBinding 有一个监听器，可以让您很好地控制绑定步骤 OnRebindCallback。您可以使用它在绑定实际做出更改之前拦截它。它没有告诉您将要进行什么更改(它还不知道)，所以它不像绑定适配器那样工作。然而，这对于[transition manager . begindelayedtransition()](https://developer.android.com/reference/android/transition/TransitionManager.html#beginDelayedTransition(android.view.ViewGroup, android.transition.Transition))来说是完美的，它必须在做出更改之前立即被调用。

Chet Haase 在上面做了很棒的开发(以及一篇关于早期版本过渡支持的[文章](/google-developers/transitions-in-the-android-support-library-8bc86a1d688e#.8ztxrqk3a))，但是我将总结一下过渡做了什么。当您调用 beginDelayedTransition()时，转换会捕获视图层次结构的状态。在下一个布局之后，转换捕获结束状态。然后，为变化的视图生成动画。

```
binding.addOnRebindCallback(**new** OnRebindCallback() {
    @Override
    **public boolean** onPreBind(ViewDataBinding binding) {
        TransitionManager.*beginDelayedTransition*(
                (ViewGroup)binding.getRoot());
        **return super**.onPreBind(binding);
    }
});
```

TransitionManager 为我创建所有的动画，所以非常容易。另一方面，当绑定表达式变脏并且有额外的开销时，就会调用这个函数。幸运的是，这通常每帧只有一次，所以如果您更改了数据模型的几个部分，您只会看到一次开销。

## 我用哪一个？

绑定适配器机制的优点:

*   精细控制—只有您想要制作动画的视图才会制作动画
*   开销少于转换(性能)
*   非常灵活——你可以创建任何你想要的动画

OnRebindCallback 机制的优点:

*   简单易用
*   不必使用自定义属性(或覆盖默认行为)
*   可以用相同的代码制作许多东西的动画(参见[过渡](https://developer.android.com/reference/android/transition/Transition.html)子类)

你可以看到它们都很有用，你甚至可以把它们结合起来。如果您这样做，转换应该[排除您想要使用绑定适配器的视图](https://developer.android.com/reference/android/support/transition/Transition.html#excludeTarget(android.view.View, boolean))。

有了这两种技术，当数据发生变化时，您将能够提供平滑的动画，让您的用户高兴。