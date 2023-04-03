# 在 Android RecyclerView 中显示 Firebase Firestore 的数据

> 原文：<https://medium.com/quick-code/display-data-from-firebase-firestore-in-android-recyclerview-db39f8c7d6b?source=collection_archive---------0----------------------->

![](img/b3cd4a6c9494e2c2bf970cf8e6cbcd99.png)

Photo by [Ruchindra Gunasekara](https://unsplash.com/@ruchindra?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](/s/photos/storage?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

在 Android 应用程序上使用 Firebase 起初可能看起来很可怕，但随着时间的推移，当你掌握它的窍门时，你会开始寻找一些工具和库，这些工具和库将在 Android 上以一些常见的布局显示你的数据。

本文将讨论在查询 Firebase Firestore 数据库中的数据后，在 Android 上实现一个回收器视图。

## Firebase 再循环适配器

这是 FirebaseUI 为 Firestore 提供的一种 RecyclerView 适配器。

它是如何工作的？它将查询结果绑定到 recyclerview，同时响应所有实时事件，即添加/删除/移动/更改的项目。

*建议在查询小结果集时使用此选项，因为所有结果都是一次加载的。*

**第一步:**按照这里[的步骤](https://firebase.google.com/docs/firestore/quickstart#java_6)在你的 Android 应用上开始使用 Firebase Firestore。这将允许您在 Firestore DB 上添加、读取和管理您的数据。

**第二步:**将**Firebase UI 库作为一个依赖项包含在你的成绩文件中。**

```
dependencies {
    // FirebaseUI for Cloud Firestore
    implementation 'com.firebaseui:firebase-ui-firestore:6.2.1'
}
```

****第三步:**按照第一步中的指导方针来[从 DB](https://firebase.google.com/docs/firestore/query-data/queries) 中读取数据，您可以将您的查询定义为:**

```
Query query = FirebaseFirestore.getInstance()
        .collection("chats")
        .orderBy("timestamp")
        .limit(50);
```

****步骤 4:** 将查询绑定到 recyclerview，如下所示:**

```
FirestoreRecyclerOptions<Chat> options = new FirestoreRecyclerOptions.Builder<Chat>()
        .setQuery(query, Chat.class)
        .build();
```

**其中，模型类可以定义为:**

****步骤 5:** 定义适配器对象，并将其附加到您的回收器视图**

```
FirestoreRecyclerAdapter adapter = new FirestoreRecyclerAdapter<Chat, ChatHolder>(options) {
    @Override
    public void onBindViewHolder(ChatHolder holder, int position, Chat model) {

    }

    @Override
    public ChatHolder onCreateViewHolder(ViewGroup group, int i) {
        // Using a custom layout called R.layout.message for each item, we create a new instance of the viewholder
        View view = LayoutInflater.from(group.getContext())
                .inflate(R.layout.message, group, false);

        return new ChatHolder(view);
    }
};//Final step, where "mRecyclerView" is defined in your xml layout as 
//the recyclerview
mRecyclerView.setAdapter(adapter);
```

****重要！！！**记得在您的活动生命周期中添加以下监听器，否则，当您运行应用程序时，您的回收器视图将不会被填充。**

```
@Override
protected void onStart() {
 super.onStart(); 
 adapter.startListening();
}@Override
protected void onStop() { 
 super.onStop(); 
 adapter.stopListening();
}
```

****！！！免责声明！！！有时，您可能会在日志跟踪中看到这个错误。****

```
com.hannah.blog.firechat W/Firestore Adapter: onEvent:error
com.google.firebase.firestore.FirebaseFirestoreException: FAILED_PRECONDITION: The query requires an index. You can create it here: [https://console.firebase.google.com/project/...](https://console.firebase.google.com/project/...)
```

**这是因为 Cloud Firestore 中的大多数复合查询都需要索引。单击错误消息中的链接，将在 Firebase 控制台中自动打开索引创建 UI，并填入正确的参数。到目前为止，我还没有找到自动解决这个问题的方法。手动点击链接效果很好。**

**特别感谢由 [Nur Rohman](https://medium.com/u/8e8c363be9c5?source=post_page-----db39f8c7d6b--------------------------------) 和 [Ankit Suda](https://medium.com/u/b828fae9c2d3?source=post_page-----db39f8c7d6b--------------------------------) 撰写的文章**

**[](https://android.jlelse.eu/fetch-data-from-firebase-cloud-firestore-to-recyclerview-using-firestorerecycleradapter-5fe6e03e32e4) [## 使用 FirestoreRecyclerAdapter 从 Firebase Cloud Firestore 获取数据以进行回收查看

### 一个月前，谷歌推出了 Firebase Cloud Firestore，这是一个完全托管的 NoSQL 移动和网络应用文档数据库…

android.jlelse.eu](https://android.jlelse.eu/fetch-data-from-firebase-cloud-firestore-to-recyclerview-using-firestorerecycleradapter-5fe6e03e32e4) [](/android-grid/how-to-use-firebaserecycleradpater-with-latest-firebase-dependencies-in-android-aff7a33adb8b) [## 如何在 Android 中使用 FirebaseRecyclerAdpater 和最新的 Firebase 依赖项

### 嘿，了不起的极客们！Ankit 带来了另一个关于 Firebase 的教程。在本教程中，您将学习如何…

medium.com](/android-grid/how-to-use-firebaserecycleradpater-with-latest-firebase-dependencies-in-android-aff7a33adb8b) 

参考资料:

[https://firebaseopensource . com/projects/firebase/firebaseui-Android/firestore/readme/](https://firebaseopensource.com/projects/firebase/firebaseui-android/firestore/readme/)**