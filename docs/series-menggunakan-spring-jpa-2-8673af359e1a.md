# Series — Menggunakan Spring JPA (2)

> 原文：<https://medium.easyread.co/series-menggunakan-spring-jpa-2-8673af359e1a?source=collection_archive---------4----------------------->

## 5.2 — JPA Repository Interfaces, Dependency Injection, Java Optional

![](img/54cbfd27612158af394821a40d27425f.png)

Photo by [Mathyas Kurmann](https://unsplash.com/@mathyaskurmann?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/contact?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# Rest API dengan Spring Boot — Series List

[**0\. Series — REST API dengan Spring Boot**](https://medium.com/easyread/series-rest-api-dengan-spring-boo-2d74060e69fb)[**1\. Series — Dimulai dari Instalasi!**](https://medium.com/easyread/series-dimulai-dari-instalasi-b564fb981d4)[**2\. Series — Inisiasi Project**](https://medium.com/easyread/series-inisiasi-project-1e37ffa951ed)[**3\. Series — Rancangan dan Konfigurasi Database dengan H2**](https://medium.com/easyread/series-rancangan-dan-konfigurasi-database-dengan-h2-3af60e66e4ef)[**4\. Series — Membuat Data Model dengan Sentuhan Lombok**](https://medium.com/easyread/series-membuat-data-model-dengan-sentuhan-lombok-af4a57a75198)[**5\. Series — Menggunakan Spring JPA (1)**](https://medium.com/easyread/series-menggunakan-spring-jpa-1-da3ea1274f7d) **6\. Series — Menggunakan Spring JPA (2) — You’re here** [**7\. Series — Inisialisasi Data**](https://medium.com/easyread/series-inisialisasi-data-aa2ae7d36691)

> Part *ini cukup panjang, harap bersabar ya teman — teman 😆*

Pada *part* sebelumnya kita sudah mendefinisikan anotasi JPA pada data *model* kita. Berikutnya kita akan membuat *repository* *interfaces* untuk dapat berinteraksi dengan data *model* kita. Kita akan meng- *extends* `**JpaRepository**` untuk dapat menggunakan *built* - *in* *method* yang telah disediakan JPA. Agar lebih paham, mari kita praktik langsung.

# Repository Layer

Pertama, buatlah sebuah *package* baru dengan nama `**repo**` **.** Package ini akan berisi *repository* *interfaces* kita. Lalu, kita buat 2 buah *interface* yaitu `**TourRepository**` dan `**TourPackageRepository**` . Kedua *interface* ini akan meng- *extends* `**JPARepository**` . *Interface* JPARepository ini memiliki 2 *parameter* , yaitu *entity* yang akan di *handle* dan *identifier* dari *entity* tersebut. *Class* `**Tour**` adalah *entity* kita, dan *identifier* (Id dari *class* `**Tour**` ) yaitu *Integer* .

JPARepository ini telah memberikan kita *method* untuk melakukan operasi **CRUD** seperti `**save()**` , `**findAll()**` , `**findOne()**` , `**delete()**` . Untuk lebih jelasnya, teman — teman dapat melihat method built-in dari JPARepository di [sini](https://docs.spring.io/autorepo/docs/spring-data-jpa/current/api/org/springframework/data/jpa/repository/support/SimpleJpaRepository.html) . Jadi dengan menggunakan JPARepository ini, kita tidak perlu repot dalam proses implementasi *method* nya, kita tinggal pakai.

Salah satu kelebihan JPA ini juga adalah *query creation from method name* , dimana kita bisa membuat *custom query method* dengan sangat mudah. Misalnya, kita ingin membuat sebuah *query* yang akan mengambil data *tour* berdasarkan harga dan waktunya. Kita bisa melakukannya dengan :

```
**List<Tour> findByPriceAndLength();** 
```

Sangat *simple* bukan menggunakan JPA? Dan tentu saja masih ada kelebihan lain dari JPA yang bisa kita gunakan. Teman-teman boleh eksplorasi lebih lanjut tentang JPA dan boleh menerapkannya dalam *project* kali ini jika ingin coba-coba. *Feel free* aja.

# Service Layer

*Nah* , setelah kita membuat *repository layer* untuk dapat berinteraksi dengan data model kita, berikutnya kita akan membuat *service layer* yang akan berisi *business logic* dari *project* kita. Mari kita buat lagi sebuah *package* baru dengan nama `**service**` . Di dalam *package* ini kita akan membuat 2 *interface* , yaitu `**TourService**` dan `**TourPackageService**` . *Interface* ini akan berisi *method* yang merupakan representasi dari *business* *logic* *project* kita. Misalnya, kita nanti akan bisa melakukan proses `**CRUD**` untuk `**Tour**` dan `**TourPackage**` . Berarti, *interface* kita bisa kita isi dengan method `**create()**` , `**find()**` , `**update()**` , `**delete()**` . Selanjutnya kita akan membuat *class* yang mengimplementasi *interface* tersebut, sehingga kita lebih mudah dan fleksibel apabila terjadi perubahan dalam *business logic project* kita.

*Interface* `**TourService**` akan kita isi dengan *method* seperti di atas dan akan kita implementasi pada *class* `**TourServiceImpl**` . Di dalam *class* `**TourServiceImpl**` ini, kita akan melakukan *repository dependency injection.*

Berikut adalah definisi *Dependency Injection* yang ditulis di [wikipedia](https://en.wikipedia.org/wiki/Dependency_injection#Dependency_injection_frameworks) :

> 在[软件工程](https://en.wikipedia.org/wiki/Software_engineering)，**依赖注入**是一种一个对象提供另一个对象的依赖的技术。依赖是可以使用的对象(一个[服务](https://en.wikipedia.org/wiki/Service_(systems_architecture)))。注入是将依赖关系传递给使用它的依赖对象(一个[客户机](https://en.wikipedia.org/wiki/Client_(computing)))。该服务是客户机状态的一部分。[【1】](https://en.wikipedia.org/wiki/Dependency_injection#cite_note-JamesShore-1)将服务传递给客户机，而不是让客户机构建或[找到服务](https://en.wikipedia.org/wiki/Service_locator_pattern)，这是该模式的基本要求。

如果我们有一个孩子，依赖注射(*)是一个很好的例子。*

*karena kita ingin menggunakan class*repository*pada*class service*kita，maka kita akan*inject*/masukkan*class repository*ter sebut ke*class service*kita。注射过程可以通过注射*级*杨注射到*级*级*级*杨进行。比如这个👇*

*我不知道，我们来自韩亚的*注射*构造者。但是，有一辆比这更好的车。与此同时，另一项研究也在进行中。我们韩亚将在今年春天完成这一任务，并且将完成组件扫描和注射。*

*现在，我们可以使用*依赖注入*和*类库*。根据依赖注入程序，我们需要实施服务。*

*Apabila teman-teman perhatikan, di sini kita juga menggunakan java optional. Optional ini bisa digunakan sebagai salah satu cara untuk menangani kasus *NullPointerException* yang biasa terjadi ketika sebuah *object* memberikan *value* ***null*** .*

*Teman-teman bisa lihat bahwa dengan menggunakan Optional, *code* tersebut menjadi lebih ringkas. Untuk kasus yang sederhana mungkin penggunaan Optional terlihat tidak jauh berbeda dengan *manual checking* seperti kasus di atas. Tapi untuk kasus yang lebih kompleks, tentu hal ini akan berbeda. Saya tidak akan bahas terlalu dalam, tetapi teman-teman boleh lihat [di sini](https://youtu.be/mucDS5Db9go) untuk lebih lanjut.*

*Berikutnya untuk *interface* `**TourPackage**` , kita akan buat seperti ini 👇*

*Dan untuk implementasi *interface* tersebut, kita akan buat *class* `**TourPackageImpl**` yang isinya seperti ini 👇*

**See you in the next part* 🔥*

## ***References :***

1.  *[Mengenai Dependency Injection](https://software.endy.muhardin.com/java/memahami-dependency-injection/) dan [Autowired](https://stackoverflow.com/questions/40620000/spring-autowire-on-properties-vs-constructor)*
2.  *[Mengenai Java Optional](https://www.baeldung.com/java-optional)*