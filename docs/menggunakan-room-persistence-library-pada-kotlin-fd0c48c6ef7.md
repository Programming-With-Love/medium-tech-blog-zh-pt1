# Menggunakan Room Persistence Library pada Kotlin

> 原文：<https://medium.easyread.co/menggunakan-room-persistence-library-pada-kotlin-fd0c48c6ef7?source=collection_archive---------2----------------------->

![](img/74f111034b19e3af06f3e5baf586d1db.png)

unsplash.com

Ketika dihadapkan pada pembuatan aplikasi yang membutuhkan penyimpanan data secara *offline* sebenarnya terdapat banyak pilihan untuk melakukannya, entah itu menggunakan *sharedpreference, sqlite,* dan lain sebagainya. Penggunaan *sharedprefence* mungkin akan menjadi pilihan pertama ketika data yang akan disimpan tidak terlalu *kompleks* atau masih berupa pasangan *value* ( *key-value* ) saja. Akan tetapi ketika dihadapkan pada situasi yang sudah mengharuskan kita untuk menyimpan suatu data yang berelasi atau lebih kompleks lagi maka *android sdk* sendiri telah menyiapkan komponen bawaan yaitu *sqlite.*

Penggunaan *Sqlite* sendiri bisa dibilang cukup merepotkan pada tahap konfigurasi awal. Sehingga diluar sana sudah banyak alternatif lain untuk melakukan proses penyimpanan data yang membuat proses konfigurasi menjadi sangat lebih mudah dan nyaman, misalnya:

*   [Realm](https://realm.io/blog/realm-for-android/)
*   [greenDao](http://greenrobot.org/greendao/)
*   [ORMlite](http://ormlite.com/sqlite_java_android_orm.shtml)
*   [Room](https://developer.android.com/topic/libraries/architecture/room)

Tentunya pilihan-pilihan diatas masing-masing memiliki kekurangan dan kelebihan masing-masing.

*Nah* , sesuai dengan judul artikel ini, saya akan membahas mengenai penggunaan *Room Persistence* pada aplikasi android. Menurut saya *Room* ini sangat membantu dan wajib untuk dipelajari, sebab *library* ini sendiri merupakan keluaran resmi dari tim android, yang berarti akan selalu mendapatkan *update* dan *support* yang bagus, apalagi *Room* merupakan bagian dari [AAC](https://developer.android.com/topic/libraries/architecture/) .

Dalam menggunakan *Room* sendiri terdapat 3 komponen penting yang harus diketahui. Layaknya *library* lain tentu kita harus mengkonfigurasinya terlebih dahulu. *Eits,* jangan khawatir konfigurasi *Room* tidaklah seribet dan sepanjang *sqlite* heheh 😃. Komponen-komponen yang perlu adalah:

*   **Entity** *Class* yang digunakan sebagai representasi dari tabel pada *database* , dideklarasikan pula kolom-kolom yang diperlukan.
*   **Database**
    *Class* yang men-extend class *RoomDatabase.* Pada *class* ini dideklarasikan *entity/* tabel yang akan dibuat dan juga versi dari *database* .
*   **DAO (Data Access Object)**
    *Interface* yang dibuat untuk mengakses data yang ada pada *database* . seperti melakukan *query* untuk *update* , *read* dll.

![](img/f46523590f27c244960560b5fea4d5a4.png)

sabar gan :)

Okeee, sampai disitu teorinya, mari langsung praktik heheh.

Untuk menggunakan *Room* diperlukan *dependency* berikut

Setelah itu, mari kita tambahkan *code* untuk *entity* /tabel. Dengan menggunakan kotlin, codenya bisa sangat singkat :)

Langkah selanjutnya adalah menambahkan *DAO* dari database kita

Oke, langkah terakhir pada tahap konfigurasi adalah menambahkan class database.

Semua konfigurasi telah selesai dilaksanakan. **Oiya** perlu untuk diperhatikan pula bahwa untuk melakukan operasi seperti memasukkan data, mengambil data dan lain-lain harus dilakukan di luar *mainthread* dan ini adalah ada aturan *Room* secara *default* . Hal ini disebakan karena melakukan *query* data pada mainthread dapat membuat aplikasi kita menjadi lambat. Walaupun room menyediakan method **allowMainThreadQueries()** namun hal ini tidak disarankan. Maka dari itu kita perlu membuat proses ini berjalan di luar mainthread. Sebenarnya banyak pilihan untuk melakukan ini seperti menggunakan Asynctask, Handler atau RxJava. Pada tutorial ini saya akan menggunakan RxJava :).

Berikut tampilan dari Activity yang mengakses data dari database

Dapat dilihat method **getAllData()** dan **insertToDb()** mengakses database menggunakan RxJava.

Untuk mengakses source code dari aplikasi ini bisa pada github repo berikut, jangan lupa *star* , *share* dan follow apabila ini bermanfaat :

[](https://github.com/muhrahmatullah/Kotlin-crud-with-Room) [## muhrahmatullah/Kotlin-crud-with-Room

### Kotlin-crud-with-Room - Sample project using kotlin and room

github.com](https://github.com/muhrahmatullah/Kotlin-crud-with-Room) 

Kedepannya saya akan mengintegrasikan repo ini dengan berbagai library populer di android seperti **Dagger2, LiveData (AAC)** dan lainnya. juga mengguanakan Approach yang lain seperti **MVP** dan **MVVM** . Jadi **stay tuned!**

Oke, sampai disini dulu untuk postingan ini, Terima kasih telah membaca sampai akhir. Kalau menurutmu post ini bermanfaat jangan lupa **follow, share dan clap ya! :)** silahkan juga komentar apabila ada yang kurang jelas atau ingin ditanyakan atau ada yang harus dikoreksi hehe!

这篇文章也发表在英国的报纸上，我想是吧！

**Refrensi :**

[](https://developer.android.com/training/data-storage/room/) [## 使用 Room | Android 开发人员将数据保存在本地数据库中

### 学习使用房间库更容易地保存数据

developer.android.com](https://developer.android.com/training/data-storage/room/) [](https://medium.com/mindorks/android-architecture-components-room-and-kotlin-f7b725c8d1d) [## Android 架构组件——Room 和 Kotlin

### 在本文中，我们将研究一个名为 Room 的架构组件库。

medium.com](https://medium.com/mindorks/android-architecture-components-room-and-kotlin-f7b725c8d1d) [](https://stackoverflow.com/questions/46602444/update-with-parameter-using-room-persistent-library) [## 使用房间永久库更新参数

### 您必须提前知道要匹配哪一列，因为 Room 不允许您动态定义…

stackoverflow.com](https://stackoverflow.com/questions/46602444/update-with-parameter-using-room-persistent-library)  [## Android 持久性代码实验室

### 架构组件是一组 Android 库，帮助您以一种健壮的方式构建应用程序…

codelabs.developers.google.com](https://codelabs.developers.google.com/codelabs/android-persistence/#0)