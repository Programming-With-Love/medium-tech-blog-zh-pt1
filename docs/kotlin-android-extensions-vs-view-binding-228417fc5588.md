# Kotlin Android 扩展与视图绑定

> 原文：<https://medium.easyread.co/kotlin-android-extensions-vs-view-binding-228417fc5588?source=collection_archive---------1----------------------->

## Perbedaan Kotlin Android 扩展 dan 视图绑定

![](img/60d18a42830ac0efcd6b4c683d75c16c.png)

Photo by [Paul Skorupskas](https://unsplash.com/@pawelskor?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/view?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

在开发 Android 应用程序的同时，一个名为“T0”的应用程序开始运行，而另一个名为“T3”的应用程序对象“T4”开始运行*布局*。第一阶段的任务是努力实现第二阶段的目标。Untuk menggantikan fungsi `**findViewById**`，terdapat beberapa cara seperti penggunaan***库 Butterknife，plugin Kotlin Android Extensions****atau yang sering dise set***合成视图*，*数据绑定*，** dan ***视图绑定*** 。*不*，这篇文章我们将讨论 *Kotlin Android 扩展*和*视图绑定*。*

# *查看绑定 dan Kotlin Android 扩展*

## *视图绑定*

*Dikutip dari [*公文*](https://developer.android.com/topic/libraries/view-binding) *，**

> ***视图绑定**是一个特性，它允许你更容易地编写与视图交互的代码。一旦在模块中启用了视图绑定，它就会为该模块中的每个 XML 布局文件生成一个绑定类。绑定类的实例包含对在相应布局中具有 ID 的所有视图的直接引用。*

*据媒体报道，view binding (*view binding (*)被认为是一种有效的方式。查看绑定方法，查看绑定*类*绑定*布局*工具。*Binding class*terse but memiliki referensi ke setiap*object*yang terda pat pada*layout/view*kita。*

## *Kotlin Android 扩展*

*Sama seperti *view binding* , metode *Kotlin Android Extensions* adalah salah satu fitur yang disediakan oleh *Kotlin* itu sendiri yang dapat kita gunakan untuk mempermudah penggunaan/pemanggilan *view* . Namun, metode *kotlin android extensions* / *synthetic view* ini tidak akan menciptakan *class* baru seperti pada *view binding* , melainkan melakukan *import* `**kotlinx.android.synthetic.<layout>**` pada *class* yang bersangkutan.*

# *Perbedaan View Binding dan Kotlin Android Extension*

*Untuk dapat memahami perbedaan dari kedua metode tersebut, kita akan membuat sebuah aplikasi yang sama dengan *layout* sebagai berikut :*

*Saya akan menambahkan *event* pada saat `**hideBtn**` diklik, yaitu `**titleTxt**` akan dihilangkan *,* dan berkebalikan untuk `**showBtn**` *.* Untuk memanggil dan menggunakannya dalam *activity* saya melakukan *invoke* `**findViewById**` secara redundan dan otomatis melakukan *import* 2 *widget* seperti *code* berikut.*

## *Implementasi menggunakan View Binding*

*Untuk dapat menggunakan *view binding* , hal yang pertama harus dilakukan adalah menambahkan *code* di bawah ini ke dalam `**build.gradle (app)**` . Jangan lupa untuk melakukan ***sync gradle*** dan lakukan ***build project*** *.**

```
*viewBinding **{** enabled = true
**}***
```

*Sehingga file `**build.gradle (app)**` menjadi seperti berikut ini.*

*Setelah melakukan perubahan pada `**build.gradle (app)**` , akan tercipta sebuah *binding class* yang mempunyai nama berdasarkan *layout* yang kita punya. Pada contoh kali ini saya mempunyai sebuah *layout* bernama `**activity_main.xml**` . Sehingga *binding class* yang tercipta akan bernama `**ActivityMainBinding**` .*

*Setelahnya kita akan *inflate layout* terhadap *activity* kita. Kita akan melakukan perubahan pada saat *inflate layout* sehingga akan menjadi seperti berikut ini :*

## *Implementasi menggunakan Kotlin Android Extensions*

> ***Note : Kotlin Android Extensions dapat digunakan pada** [**Android Studio 3.6**](https://developer.android.com/studio/preview) **ke atas.***

*Untuk dapat menggunakan *kotlin android extensions* / *synthtetic view* , hal yang pertama harus dilakukan adalah menambahkan plugin di bawah ini ke dalam `**build.gradle**` . Jangan lupa untuk melakukan ***sync gradle*** setelah menambahkan plugin.*

```
*apply plugin: 'kotlin-android-extensions'*
```

*Setelah menambahkan plugin, *kotlin android extensions* dapat digunakan dengan langsung memanggil *id* dari *object view.* Sehingga *activity* akan tampak seperti berikut.*

*Dengan menggunakan *view binding* , terlihat otomatis akan melakukan import `**kotlinx.android.synthetic.activity_main.xml**` dan tidak melakukan *invoke* terhadap `**findViewById**` yang redundan. Berbeda dengan *view binding* , dengan menggunakan *kotlin android extensions code* akan jauh terlihat *clean* dan simpel karena tidak perlu menggunakan sebuah *binding class* .*

## ****Menggunakan findViewById()****

## ****Menggunakan View Binding****

## ****Menggunakan Kotlin Android Extensions****

# *Kesimpulan*

*Dengan melihat perbandingan sebelum dan sesudah penggunaan *view binding* dan *kotlin android extensions,* dapat disimpulkan beberapa hal:*

## *1\. Import Type Safety*

*Dengan menggunakan *View Binding* maupun *Kotlin Android Extensions* , kita tidak perlu lagi melakukan *import object type* karena telah di- *cast* ke tipe masing-masing objek. Sehingga tidak ada lagi kesalahan dalam import yang sering kali terjadi ketika kita menggunakan `**findViewById()**` .*

## *2\. Layout Changes*

*Dengan menggunakan *View Binding* , untuk dapat menggunakan *object view* yang telah kita tambahkan, kita harus melakukan ***build project*** terlebih dahulu, sedangkan ketika menggunakan *Kotlin Android Extensions* dapat langsung digunakan tanpa *build project* terlebih dahulu. Sehingga *Kotlin Android Extensions* lebih menguntungkan.*

## *3\. Layout Import*

*Dengan menggunakan *View Binding* , kita akan melakukan *import binding class* yang mempunyai nama yang sama dengan *layout* kita. Sedangkan menggunakan *Kotlin Android Extensions* , kita melakukan *import synthetic view* terhadap *layout* yang kita gunakan. Dalam hal ini, melakukan pengecekan terhadap *import class* akan lebih memungkinkan dan mudah dilakukan, dibandingkan harus melakukan pengecekan terhadap *import syntetic view* . Sehingga *View Binding* lebih lebih menguntungkan.*

## *4\. Clean & Boilerplate Code*

*Dibandingkan *View Binding* penggunaan *Kotlin Android Extensions* akan lebih membuat *code* kita tampak *clean* seperti yang sudah kita lihat pada *code* di atas karena tidak perlu menambahkan *extra code* seperti ketika menggunakan *View Binding* maupun `**findViewById**` .*

# *Referensi*

*Untuk mempelajari lebih lanjut tentang V *iew Binding* dan *Kotlin Android Extensions* , rekan-rekan dapat melihatnya pada *official docs* di bawah ini.*

*[](https://kotlinlang.org/docs/reference/android-overview.html) [## Kotlin for Android - Kotlin Programming Language

### Android mobile development has been Kotlin-first since Google I/O in 2019\. Using Kotlin for Android development, you…

kotlinlang.org](https://kotlinlang.org/docs/reference/android-overview.html) [](https://developer.android.com/topic/libraries/view-binding) [## View Binding | Android Developers

### View binding is a feature that allows you to more easily write code that interacts with views. Once view binding is…

developer.android.com](https://developer.android.com/topic/libraries/view-binding) 

Happy Coding ❣️*