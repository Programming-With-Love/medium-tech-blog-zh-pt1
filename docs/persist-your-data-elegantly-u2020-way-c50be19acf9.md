# 优雅地保存您的数据:U2020 方式

> 原文：<https://medium.com/google-developer-experts/persist-your-data-elegantly-u2020-way-c50be19acf9?source=collection_archive---------1----------------------->

SharedPreferences 非常棒，是在 Android 上持久保存用户数据的最简单方法。它允许您以键值对的形式存储/访问基本的偏好数据。支持存储/访问原始数据: **boolean、float、int、long、String、 *StringSet (*** *不巧不是数组)。*

几乎所有安卓应用都用。尽管 API 的使用很简单，但它也有自己的问题。当您的代码库增长时，它变得很容易出错，因为它是初始化的，并与字符串文字一起使用。

我将首先谈论访问*shared preferences*API 的传统方法，并讨论所有常见方法的问题。当代码库变得更大时，这些问题尤其重要。

然后，我将给出另一种更简洁的方法，即使用依赖注入。我在杰克·沃顿[的一个名为](https://github.com/JakeWharton/) [U2020](https://github.com/JakeWharton/u2020) 的开源示例应用中遇到了这种方法。它使用 *Dagger* 来访问 *SharedPreferences* 。我们将在本帖中使用*匕首 2* 。它是为 Java 和 Android 设计的最快的依赖注入库。

如果你已经在使用 *Dagger，*你会发现一种更干净安全的方式来访问 *SharedPreferences。*如果你没有，并且想学习 *Dagger 2，*这是一篇很棒的帖子，因为它通过提供 Dagger 的真实使用案例展示了它的用处。我保证它没有咖啡，加热器和泵的例子☺

# 传统方式

您首先需要访问一个 *SharedPreferences* 类的实例。它可以通过两种不同的方式获得:

```
//Use a static helper method.
PreferenceManager.*getDefaultSharedPreferences*(Context context);//Or use a method in your Context
getSharedPreferences(String name, int mode)
```

[第一个方法](http://developer.android.com/reference/android/preference/PreferenceManager.html#getDefaultSharedPreferences(android.content.Context))给出了默认的 *SharedPreferences* ，它将数据保存到一个名为 app 包名的文件中。使用[第二种方法](http://developer.android.com/reference/android/content/Context.html#getSharedPreferences(java.lang.String, int))，您可以选择提供首选项文件的文件名和访问它的模式。

最后，您可以获得如下数据:

```
String myValue = prefs.getString("myKey", "deafultValue");
```

这里的问题是，每次使用时，您都必须提供一个*字符串*键和一个默认值。大多数开发人员不会为这些键使用一个*常量*文件，而是一遍又一遍地输入它们，这使得它容易出错。

这就是为什么我在过去用如下方法创建了一个单独的 *PrefUtils* 类来防止错误:

```
PrefUtils.getMyValue();
PrefUtils.getMyValue(defaultValue);
```

拥有一个用于访问 *SharedPreferences* 的实用程序类也有它自己的问题。首先，当项目越来越大时，维护它就越来越困难。其次，由于您使用的是*上下文，*如果没有正确实现，就有导致内存泄漏的风险。我见过这个 *PrefUtils* 类的许多不同实现。

最后，当你想存储一些东西时，你会这样做:

```
prefs.edit().putString("myKey", "newValue").apply();
```

您可能会意识到，我们在这里进行一连串的调用来存储数据。对于 store 操作，您必须首先使用 *edit()* 方法(这将为您提供 *SharedPreferences。Editor* object)然后进行更改，最后使用 *apply()或 commit()* 方法。 [*共享优先。编辑器*](http://developer.android.com/reference/android/content/SharedPreferences.Editor.html) 对象确保首选项值保持一致的状态。

尽管有 lint 检查，但您可能会忘记在最后添加 *apply()* 方法。而且你会花上几个小时去找出为什么你的数据没有保存下来。

# U2020 方式

我非常喜欢这种方法，并开始在我的新项目中使用。它保护你不犯错误，使你的代码更干净。当你用匕首和*配合使用时，效果尤其好。*

## 优点是:

*   键和默认值在构造函数中给出。
*   不必将它们保存在单独的*常量*文件中，因为它们只被创建一次。
*   防止意外错误。
*   像 *set，get，isSet 这样漂亮简单的方法。*
*   存储 [*枚举*](https://github.com/vngrs/PomoPomoAndroid/blob/develop/shared/src/main/java/com/vngrs/android/pomodoro/shared/data/prefs/EnumPreference.java) 或更复杂的[自定义数据。](https://github.com/vngrs/PomoPomoAndroid/blob/develop/shared/src/main/java/com/vngrs/android/pomodoro/shared/data/prefs/DateTimePreference.java)

下面是一个*字符串引用:*的代码

```
public class StringPreference {
  private final SharedPreferences preferences;
  private final String key;
  private final String defaultValue; ... public StringPreference(@NonNull SharedPreferences preferences, 
                          @NonNull String key, 
                          @Nullable String defaultValue) {
    this.preferences = preferences;
    this.key = key;
    this.defaultValue = defaultValue;
  } @Nullable
  public String get() {
    return preferences.getString(key, defaultValue);
  } public boolean isSet() {
    return preferences.contains(key);
  } public void set(@Nullable String value) {
    preferences.edit().putString(key, value).apply();
  } public void delete() {
    preferences.edit().remove(key).apply();
  }
}
```

然后当你需要读/写你的数据时，你可以这样使用它:

```
StringPreference myPref = 
        new StringPreference(prefs, "myKey*", "defaultValue"*);String oldValue = mPref.get();
mPref.set("newValue");
```

这样做的一个缺点是，您必须为每个想要持久化的值实例化这个对象。耐心点！匕首会来救你的。

## 匕首 2 怎么用这些职业？

在 *Dagger 中，*你需要有模块来提供你要注入的对象。几乎所有的 Android Dagger 项目都有*应用模块*，在那里你提供*应用*特定对象。(例如 *Application，NotificationManager，GoogleApiClient* )你可以在[这里找到一个例子](https://github.com/pomopomo/WearPomodoro/blob/develop/mobile/src/main/java/com/tasomaniac/android/pomodoro/AppModule.java)。

您的应用程序中可能也会有一个 [*数据模块*](https://github.com/pomopomo/WearPomodoro/blob/develop/shared/src/main/java/com/tasomaniac/android/pomodoro/shared/data/DataModule.java) 。所以最好在里面提供 *SharedPreferences* 。

```
@Module
public final class DataModule { @Provides @Singleton
  SharedPreferences provideSharedPreferences(Application app) {
    return PreferenceManager.*getDefaultSharedPreferences*(app);
  }
  ...
}
```

注意，我们把它变成了*单例。这样，*provideSharedPreferences*方法将只被调用一次。*

*应用*对象*对象*由于在 *AppModule 中提供，所以 *Dagger* 会自动注入到这里。*还要注意，我们在这里使用*应用程序*作为*上下文*来防止内存泄漏问题。

现在我们来看一个 *PrefsModule:*

```
@Module(includes = DataModule.class)
public class PrefsModule {

  ... @Provides @Singleton
  StringPreference provideMyValue(SharedPreferences prefs) {
    return new StringPreference(prefs, "myKey*", "defaultValue"*);
  }
}
```

我们可以在这里提供我们所有的首选项，以便在整个应用程序中使用。在此模块中，只能一次提供关键字和默认值。

最后，使用*注入*注释来访问它。当您这样做时，将只使用一个单例实例。

```
public class MainActivity extends Activity {

  @Inject StringPreference myPreference; @Override
  protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState); String value = myPreference.get(); }
}
```

我们已经为您想要持久化的每种数据类型创建了单独的类。然后我们有 2 个匕首模块。这看起来似乎有点多，但总体来说，我认为这是一种优雅且安全的使用 *SharedPreferences* 的方式。

# 附录

我的开源 Wear Pomodoro 应用程序中我使用了这种技术:[https://github.com/pomopomo/WearPomodoro/](https://github.com/pomopomo/WearPomodoro/)

学习 Dagger 的优秀教程和视频:

*   杰克·沃顿在 Devoxx 和[滑梯甲板上的](https://speakerdeck.com/jakewharton/dependency-injection-with-dagger-2-devoxx-2014)[会议。](https://www.parleys.com/tutorial/5471cdd1e4b065ebcfa1d557/)
*   [Gregory Kick 的更多技术视频](https://www.youtube.com/watch?v=oK_XtfXPkqw)
*   【TutPlus 的教程
*   当然还有 U2020 应用程序，尽管它仍然使用 Dagger 1。

关注我的[@塔斯曼尼亚克](https://twitter.com/tasomaniac)和 [+SaidTahsinDane](https://plus.google.com/+SaidTahsinDane/posts)