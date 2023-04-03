# Android MVP Series : Implementasi Butter Knife dan Dagger pada Aplikasi Android

> 原文：<https://medium.easyread.co/android-mvp-series-implementasi-butter-knife-dan-dagger-pada-aplikasi-android-f46d4cb42285?source=collection_archive---------1----------------------->

## Apa itu Butter Knife dan Dagger ?

Pada tulisan sebelumnya, [**Android MVP Series : Membangun Aplikasi Android dengan Arsitektur MVP**](https://medium.com/easyread/android-mvp-series-membangun-aplikasi-android-dengan-arsitektur-mvp-fbf1f77ecaec) , saya menyinggung penggunaan *library* ***Butter Knife*** dan ***Dagger*** untuk memudahkan membuat aplikasi Android.

Sebenarnya apa itu ***Butter Knife*** dan ***Dagger*** ?

![](img/17ee17013da527960a2cafe9aa610200.png)

Image from [Pixabay](https://pixabay.com/en/question-mark-why-problem-solution-2123967/) ([TeroVesalainen](https://pixabay.com/en/users/TeroVesalainen-809550/))

# Butter Knife

![](img/c22f568dae2f7a4d53c5a0ab4b02367e.png)

Butter knife image taken from Google Image

[**Butter Knife**](http://jakewharton.github.io/butterknife/) merupakan *library* yang membantu kita untuk menyederhanakan penulisan komponen view di Android. Untuk memakai *library* Butter Knife ini, kamu hanya perlu menambahkan `**compile ‘com.jakewharton:butterknife:8.8.1’**` pada file *gradle-* mu. Contoh pemakaian.

Biasanya saat kita mendeklarasikan sebuah komponen view seperti contoh diatas, maka kita akan memakai ***findViewById*** . *Nah* , dengan memakai butter knife maka penulisannya akan lebih sederhana.

```
// Dengan findViewById
private ImageView imageView;
private TextView titleView;imageView = (ImageView) view.findViewById(R.id.image)
titleView = (TextView) view.findViewById(R.id.title)
```

Penulisan dan proses *injection view* diatas akan berubah menjadi

```
@Bind(R.id.image) ImageView imageView;
@Bind(R.id.title) TextView titleView;// pada create view, tambahkan
ButterKnife.*bind*(this, itemView);
```

Jadi, selain lebih sederhana, barisan kode untuk instansiasi objek akan lebih sedikit. Dan pada `onDestroy()` atau `onDestroyView()` kita dapat meng- *unbind view* yang sebelumnya untuk men- *destroy itemview* yang kita pakai tadi menggunakan `**ButterKnife.*unbind*(this);**`Selain proses *binding view* diatas, kita juga dapat melakukan *binding listener* pada aplikasi kita, seperti.

```
@OnClick(R.id.*login_button*)
public void onLoginButtonClick() {
    // doing something
}
```

Untuk beberapa *method* dan penjelasan lainnya mengenai Butter Knife sendiri dapat kamu lihat pada [link](http://jakewharton.github.io/butterknife/) ini.

# Dagger

![](img/737e1477e94f6d20f8fe85ae39451beb.png)

Dagger Image taken from Google Image

[**Dagger**](https://google.github.io/dagger/) , merupakan *library* yang digunakan untuk proses penerapan ***Dependency Injection*** pada aplikasi android dan Java. *Nah,* apa itu Dependency Injection?

> [Dependency injection](https://en.wikipedia.org/wiki/Dependency_injection) *adalah sebuah* design pattern *yang digunakan untuk menulis kode program yang lebih sederhana yaitu tentang bagaimana mengirimkan objek ke dalam sebuah* class *yang membutuhkannya (* loosely-coupled *).* Dependency *ialah objek yang akan digunakan atau di kirim, dan* Injection *adalah pengiriman objek A ke objek yang membutuhkan.*

*Hm* , agak membingungkan. Mari kita lihat contoh. Misalnya, kita memiliki sebuah *class* `FilmRepository` dimana *class* ini akan dipakai oleh beberapa *class* seperti `FilmPresenter` . Jika kita tidak menggunakan konsep DI, maka kita akan membuatnya seperti ini.

```
class FilmPresenter {
   FilmRepository repository = new FilmRepository(); void getData() {
      repository.getData();
   }
}
```

Sekilas tidak ada yang salah pada implementasinya, tetapi jika nanti `FilmRepository` ini akan dipakai banyak *class* , dan terjadi perubahan, maka akan banyak file yang perlu diubah dan diperbaiki. Maka kode akan semakin kotor.
**Jadi bagaimana jika kita menerapkan konsep *Dependency Injection* ?**

**A. Manual Dependency Injection** Dependency Injection dapat kita terapkan secara manual. Misalnya.

```
class FilmActivity extends Activity {
   FilmRepository repository; @Override
   public void onCreate() {
      repository = new FilmRepository(); // pass repository ke presenter
      presenter = new MyPresenter(repository);
   }
}class FilmPresenter {
   private FilmRepository repository; public FilmPresenter(FilmRepository repository) {
            this.repository = repository;
   } void getData() {
      repository.getData();
   }
}
```

Maka presenter tidak perlu mengetahui bagaimana objek repository terbentuk, ia hanya perlu memakai saja. Akan tetapi hal ini juga dapat menjadi sebuah masalah. Bagaimana jika `Presenter` membutuhkan banyak objek, atau bagaimana jika `Repository` tadi juga memerlukan objek yang lain. Apakah kita harus membuat semua implementasinya pada Activity? Tentu saja hal ini juga akan menambah kerumitan kode. *Nah* , untuk mengatasi ini, disinilah kita memakai *Dagger* sebagai penolong kita untuk menerapkan *Dependency Injection* .

**B. Dagger** Dependency Injection dengan dagger memakai *library* *Dagger 2\.* Untuk memakai *library* ini, pastikan untuk menambahkan *script* ini pada file *build.gradle.*

```
compile 'com.google.dagger:dagger:2.12'
apt 'com.google.dagger:dagger-compiler:2.12'
provided 'javax.annotation:jsr250-api:1.0'
```

Pada implementasi *dagger* ini, dikenal beberapa istilah yaitu **Module, Component** dan **Graph** . Module ialah sebuah *class* yang akan menjadi *provider* atau penyedia dari *dependency class* yang akan dipakai. Sedangkan Component adalah *class* yang akan menjadi wadah pendaftaran module-module yang dibuat. Dan Graph adalah sebuah *interface* yang berisikan *class-class* yang akan memakai *dependency class* tersebut.

**Module** Katakan kita akan memiliki 3 buah module, yaitu *AppModule, DataModule* dan *ApiModule* . Dimana AppModule akan menjadi penyedia Application kita, DataModule akan menjadi penyedia repository dan data source aplikasi kita, dan ApiModule akan menjadi penyedia konfigurasi Api dan service Api kita.

Dapat dilihat, saya membagi module-module tersebut sesuai dengan tujuan dan kebutuhan. Kamu boleh menambahkan module lain sesuai kebutuhan.

**Component** Setelah itu, kita daftarkan module-module tersebut kedalam sebuah *class* yang saya beri nama *AppComponent* . Setiap module yang kamu buat harus kamu tambahkan di file ini agar module tersebut dikenali.

**Graph** Lalu setiap Activity, Fragment atau *class* yang akan memakai *dependency class* tersebut didaftarkan pada *Graph* . Method `inject` inilah yang akan dipakai nantinya untuk proses injeksi *dependency class* .

Selain ketiga hal diatas, kita juga perlu menambahkan *builder* nya agar ketika di *build* file yang disediakan dapat digunakan.

Maka nanti impelementasi di Activity atau Fragment atau *class* lain akan menjadi seperti ini.

```
class FilmActivity extends Activity {
   **@Inject FilmRepository repository;** @Override
   public void onCreate() {
      **Injector.*obtain*(getActivity()).inject(this);** // pass repository ke presenter
      presenter = new MyPresenter(repository);
   }
}class FilmPresenter {
   private FilmRepository repository; public FilmPresenter(FilmRepository repository) {
            this.repository = repository;
   } void getData() {
      repository.getData();
   }
}
```

Jika terjadi perubahan pada *repository* atau *dependency class* yang lain, maka kita hanya perlu mengganti di module yang bersangkutan sehingga kode tetap terjaga dan bersih.

*Nah* , kira-kira begitulah gambaran dari implementasi *butterknife* dan *dagger* yang saya gunakan pada aplikasi saya. Untuk pengembangan selanjutnya, dapat kamu lihat pada repo ini [**memorize**](https://github.com/eminartiys/memorize) . Semoga artikel ini bermanfaat bagi kamu yang ingin mencoba. Kamu juga dapat melihat beberapa sumber dan implementasikan sesuai yang kamu mengerti. *Happy Learning!* 😃

*如果您有任何问题或抱怨，请随时联系我这些社交网络:* [*twitter*](https://twitter.com/eyseminarti) *，*[*LinkedIn*](http://linkedin.com/in/eminarti-sianturi-08a369102)*，或*[*email*](mailto:eminartiys@gmail.com)*。能答的我尽量答，不然我们一起学:)。*

## 参考 I

1.  [https://github.com/googlesamples/android-architecture](https://github.com/googlesamples/android-architecture)
2.  [https://caster.io/courses/dagger2](https://caster.io/courses/dagger2)
3.  [http://jakewharton.github.io/butterknife/](http://jakewharton.github.io/butterknife/)
4.  [https://google.github.io/dagger/](https://google.github.io/dagger/)