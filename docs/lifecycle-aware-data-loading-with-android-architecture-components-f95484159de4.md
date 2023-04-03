# 使用架构组件加载生命周期感知数据

> 原文：<https://medium.com/androiddevelopers/lifecycle-aware-data-loading-with-android-architecture-components-f95484159de4?source=collection_archive---------0----------------------->

在我之前的博文[中，我谈到了如何使用](/google-developers/making-loading-data-on-android-lifecycle-aware-897e12760832)[加载器](https://developer.android.com/guide/components/loaders.html)以自动处理配置变更的方式加载数据。

随着[架构组件](https://developer.android.com/topic/libraries/architecture/index.html)的引入，有一个替代方案为这个用例提供了一个现代的、灵活的、可测试的解决方案。

# 关注点分离

装载机的两大优势是:

*   它们封装了数据加载的过程
*   它们在配置更改后仍然存在，避免了不必要的重新加载数据

对于架构组件，这两个好处现在由两个独立的类来处理:

*   `[LiveData](https://developer.android.com/topic/libraries/architecture/livedata.html)`提供一个生命周期感知基类，用于封装装载数据
*   `[ViewModels](https://developer.android.com/topic/libraries/architecture/viewmodel.html)`在配置变更时自动保留

这种分离的一个显著优点是，您可以在多个`ViewModels`中重用同一个`LiveData`，通过一个`[MediatorLiveData](https://developer.android.com/reference/android/arch/lifecycle/MediatorLiveData.html)`将多个`LiveData`源组合在一起，或者在一个`Service`中使用它们，避免试图将一个`Loader`合并到一个没有`LoaderManager`的场景中。

虽然`Loaders`支持用户界面和数据加载的分离(可测试应用的第一步！)，这个模型扩展了这个优势——您的`ViewModel`可以通过模拟您的数据源来完全测试，而`LiveData`可以在完全隔离的情况下测试。一个干净的、可测试的架构是[应用架构指南](https://developer.android.com/topic/libraries/architecture/guide.html)中的一大焦点。

# 保持简单

这在理论上听起来不错。一个说明性的例子[重现我们的](/google-developers/making-loading-data-on-android-lifecycle-aware-897e12760832#5aa5) `[AsyncTaskLoader](/google-developers/making-loading-data-on-android-lifecycle-aware-897e12760832#5aa5)`可能有助于使想法更具体一点:

```
public class JsonViewModel extends AndroidViewModel {
  // You probably have something more complicated
  // than just a String. Roll with me
  private final MutableLiveData<List<String>> data =
      new MutableLiveData<List<String>>(); public JsonViewModel(Application application) {
    super(application);
    loadData();
  } public LiveData<List<String>> getData() {
    return data;
  } private void loadData() {
    new AsyncTask<Void,Void,List<String>>() {
      @Override
      protected List<String> doInBackground(Void... voids) {
        File jsonFile = new File(getApplication().getFilesDir(),
            "downloaded.json");
        List<String> data = new ArrayList<>();
        // Parse the JSON using the library of your choice
        return data;
      } @Override
      protected void onPostExecute(List<String> data) {
        this.data.setValue(data);
      }
    }.execute();
  }
}
```

等等，一个`AsyncTask`？这怎么安全？这里有两个安全功能:

1.  `[AndroidViewModel](https://developer.android.com/reference/android/arch/lifecycle/AndroidViewModel.html)`(`ViewModel`的子类)只有一个对应用程序`Context`的引用，所以非常重要的是我们没有引用`Activity`的`Context`等等。这可能会出现泄漏——甚至有一个棉绒检查来避免这种问题。
2.  只有当有东西在观察它的时候，才会给出结果

但是我们还没有完全抓住架构组件的本质:我们的`ViewModel`直接构建和管理我们的`LiveData`。

```
public class JsonViewModel extends AndroidViewModel {
  private final JsonLiveData data; public JsonViewModel(Application application) {
    super(application);
    data = new JsonLiveData(application);
  } public LiveData<List<String>> getData() {
    return data;
  }
}public class JsonLiveData extends LiveData<List<String>> {
  private final Context context; public JsonLiveData(Context context) {
    this.context = context;
    loadData();
  } private void loadData() {
    new AsyncTask<Void,Void,List<String>>() {
      @Override
      protected List<String> doInBackground(Void… voids) {
        File jsonFile = new File(getApplication().getFilesDir(),
            "downloaded.json");
        List<String> data = new ArrayList<>();
        // Parse the JSON using the library of your choice
        return data;
      } @Override
      protected void onPostExecute(List<String> data) {
        setValue(data);
      }
    }.execute();
  }
}
```

因此，正如您所料，我们的`ViewModel`变得相当简单。我们的`LiveData`现在完全封装了加载过程，只加载一次数据。

# `LiveData`世界中的数据变化

就像[加载器如何对其他地方的变化做出反应一样](/google-developers/making-loading-data-on-android-lifecycle-aware-897e12760832#1e8c)，这个相同的功能在使用`LiveData`时非常关键——顾名思义，数据是会变化的！当有一个观察者时，我们可以很容易地修改我们的类来继续加载数据:

```
public class JsonLiveData extends LiveData<List<String>> {
  private final Context context;
  private final FileObserver fileObserver;public JsonLiveData(Context context) {
    this.context = context;
    String path = new File(context.getFilesDir(),
        "downloaded.json").getPath();
    fileObserver = new FileObserver(path) {
      @Override
      public void onEvent(int event, String path) {
        // The file has changed, so let’s reload the data
        loadData();
      }
    };
    loadData();
  } @Override
  protected void onActive() {
    fileObserver.startWatching();
  } @Override
  protected void onInactive() {
    fileObserver.stopWatching();
  } private void loadData() {
    new AsyncTask<Void,Void,List<String>>() {
      @Override
      protected List<String> doInBackground(Void… voids) {
        File jsonFile = new File(getApplication().getFilesDir(),
            "downloaded.json");
        List<String> data = new ArrayList<>();
        // Parse the JSON using the library of your choice
        return data;
      } @Override
      protected void onPostExecute(List<String> data) {
        setValue(data);
      }
    }.execute();
  }
}
```

既然我们对监听变化感兴趣，我们可以利用 LiveData 的`onActive()`和`onInactive()`回调，只在我们的数据上有主动观察者时才进行监听——只要有人在观察，就可以保证他们得到最新的数据。

# 观察数据

在`Loader`世界中，将数据放入用户界面需要用到一个`LoaderManager`，在正确的地方调用`initLoader()`，并构建一个`LoaderCallbacks`。在架构组件的世界里，事情要简单得多。

我们需要做两件事:

1.  获取对我们的视图模型的引用
2.  开始观察我们的实时数据

但是解释起来几乎和代码本身一样长:

```
public class MyActivity extends AppCompatActivity {
  public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    JsonViewModel model =
        ViewModelProviders.of(this).get(JsonViewModel.class);
    model.getData().observe(this, data -> {
      // update UI
    });
  }
}
```

你会注意到没有必要事后清理:`ViewModels`只在需要的时候自动存活，`LiveData`只在调用`Activity` / `Fragment` / `[LifecycleOwner](https://developer.android.com/topic/libraries/architecture/lifecycle.html#lco)`开始或恢复时自动传递数据给你。

# 装载所有的东西

现在，如果你还在强烈反对`AsyncTask`的阵营中，我完全可以接受:`LiveData`比仅仅局限于那个构造要灵活得多。

例如， [Room](https://developer.android.com/topic/libraries/architecture/room.html) 让您拥有[可观察查询](https://developer.android.com/topic/libraries/architecture/room.html#daos-query-observable)——返回`LiveData`的数据库查询，这样数据库更改就可以通过您的视图模型自动传播到您的 UI。有点像没有接触光标或加载器的`CursorLoader`。

我们也可以用一个`LiveData`类重写`[FusedLocationApi](/google-developers/making-loading-data-on-android-lifecycle-aware-897e12760832#85b1)` [示例](/google-developers/making-loading-data-on-android-lifecycle-aware-897e12760832#85b1):

```
public class LocationLiveData extends LiveData<Location> implements
    GoogleApiClient.ConnectionCallbacks,
    GoogleApiClient.OnConnectionFailedListener,
    LocationListener {
  private GoogleApiClient googleApiClient; public LocationLiveData(Context context) {
    googleApiClient =
      new GoogleApiClient.Builder(context, this, this)
      .addApi(LocationServices.API)
      .build();
  } @Override
  protected void onActive() {
    // Wait for the GoogleApiClient to be connected
    googleApiClient.connect();
  } @Override
  protected void onInactive() {
    if (googleApiClient.isConnected()) {
      LocationServices.FusedLocationApi.removeLocationUpdates(
          googleApiClient, this);
    }
    googleApiClient.disconnect();
  } @Override
  public void onConnected(Bundle connectionHint) {
    // Try to immediately find a location
    Location lastLocation = LocationServices.FusedLocationApi
        .getLastLocation(googleApiClient);
    if (lastLocation != null) {
      setValue(lastLocation);
    } // Request updates if there’s someone observing
    if (hasActiveObservers()) {
      LocationServices.FusedLocationApi.requestLocationUpdates(
          googleApiClient, new LocationRequest(), this);
    }
  } @Override
  public void onLocationChanged(Location location) {
    // Deliver the location changes
    setValue(location);
  } @Override
  public void onConnectionSuspended(int cause) {
    // Cry softly, hope it comes back on its own
  } @Override
  public void onConnectionFailed(
      @NonNull ConnectionResult connectionResult) {
    // Consider exposing this state as described here:
    // [https://d.android.com/topic/libraries/architecture/guide.html#addendum](https://d.android.com/topic/libraries/architecture/guide.html#addendum)
  }
}
```

# 仅仅触及架构组件的表面

Android 架构组件还有很多，所以一定要查看所有的文档。

我个人强烈建议通读整个[应用架构指南](https://developer.android.com/topic/libraries/architecture/guide.html)，让你了解所有这些组件如何组合在一起，为你的整个应用形成一个坚实的架构。