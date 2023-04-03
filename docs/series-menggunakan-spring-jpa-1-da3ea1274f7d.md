# Series — Menggunakan Spring JPA (1)

> 原文：<https://medium.easyread.co/series-menggunakan-spring-jpa-1-da3ea1274f7d?source=collection_archive---------3----------------------->

## 5 .1— Use Spring JPA in Data Model

![](img/6f8a6c0719a39f369c2a0e68a2ffda93.png)

Photo by [israel palacio](https://unsplash.com/@othentikisra?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/connection?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# Rest API dengan Spring Boot — Series List

[**0\. Series — REST API dengan Spring Boot**](https://medium.com/easyread/series-rest-api-dengan-spring-boo-2d74060e69fb)[**1\. Series — Dimulai dari Instalasi!**](https://medium.com/easyread/series-dimulai-dari-instalasi-b564fb981d4)[**2\. Series — Inisiasi Project**](https://medium.com/easyread/series-inisiasi-project-1e37ffa951ed)[**3\. Series — Rancangan dan Konfigurasi Database dengan H2**](https://medium.com/easyread/series-rancangan-dan-konfigurasi-database-dengan-h2-3af60e66e4ef)[**4\. Series — Membuat Data Model dengan Sentuhan Lombok**](https://medium.com/easyread/series-membuat-data-model-dengan-sentuhan-lombok-af4a57a75198) **5\. Series — Menggunakan Spring JPA (1) — You’re here** [**6\. Series — Menggunakan Spring JPA (2)**](https://medium.com/easyread/series-menggunakan-spring-jpa-2-8673af359e1a)[**7\. Series — Inisialisasi Data**](https://medium.com/easyread/series-inisialisasi-data-aa2ae7d36691)

Hai! Pada *part* kali ini, kita akan membahas sedikit mengenai JPA.

Dulu, apabila kita ingin mengakses *database* biasanya kita menggunakan **JDBC ( *Java Database Connectivity* )** , dimana proses penggunaan JDBC ini cukup panjang. Pertama kita harus membuat koneksi terlebih dahulu, selanjutnya memastikan koneksi apakah sudah aman dengan menggunakan *Exception* dan bagian yang lebih panjang lagi adalah ketika kita membuat *query* secara manual, baik dengan *statement* ataupun *preparedStatement* . *Nah,* untuk mengatasi masalah tersebut, kita dapat menggunakan JPA.

[**JPA**](https://www.baeldung.com/jpa-entities) akan secara otomatis memetakan *class* yang kita miliki ke *table* yang ada di *database* . Teknologi ini biasa disebut ORM( *Object-Relational Mapping* ). Agar lebih jelas, mari kita coba langsung.

JPA Entity adalah sebuah *class* yang merepresentasikan *table* yang ada di *database* . Berarti, *class* `**Tour**` yang kita miliki dapat kita gunakan sebagai sebuah *entity* yang merepresentasikan *table* `**tour**` pada *database* kita. Untuk menggunakan JPA Entity ini, kita hanya perlu memberikan anotasi `**@Entity**` pada *class* `**Tour**` .

Secara otomatis, nama dari *entity* akan disesuaikan dengan nama *class* tersebut. Dengan menambahkan anotasi `**@Entity**` tersebut, artinya *class* `**Tour**` kita sudah di- *mapping* dengan *table* `**tour**` . Berikutnya, layaknya sebuah *table* , anotasi *entity* ini juga harus memiliki sebuah *primary* *key* yang ditandai dengan anotasi `**@Id**` . Jika kita ingin *primary key* yang kita miliki di- *generate* secara otomatis, maka kita bisa menggunakan anotasi `**@GeneratedValue**` . Anotasi `**@GeneratedValue**` ini memiliki 4 jenis strategi untuk *generate primary key* , yakni ***AUTO, TABLE, SEQUENCE*** dan ***IDENTITY.*** Untuk penjelasan mengenai masing-masing jenis strategi tersebut, teman-teman bisa baca di [sini](https://www.baeldung.com/jpa-entities) .

Biasanya kita ingin men- *generate primary key* secara *auto increment* , maka kita bisa gunakan strategi **IDENTITY.** Selain itu kita juga bisa menggunakan anotasi `**@Column**` untuk *class* properti yang kita miliki agar lebih spesifik. Misalnya untuk sebuah *description* , kita ingin *column* tersebut bisa menampung 2000 *character* , maka kita dapat spesifikkan dengan cara :

```
*@Column*(name="description", length = 2000)
*public* String description;
```

Berikutnya, kita akan membuat *relationship* antar *entity* . Seperti yang sudah kita desain sebelumnya, *class* `**TourPackage**` dan *class* `**Tour**` memiliki relasi *one-to-many* . Satu *TourPackage* akan memiliki banyak *Tour* . Untuk menghubungkan kedua *entity* ini, kita hanya perlu menambahkan anotasi `**@OneToMany**` dan anotasi `**@ManyToOne**` . Anotasi `**@OneToMany**` akan kita definisikan di *class* `**TourPackage**` dan anotasi `**@ManyToOne**` kita definisikan di *class* `**Tour**` . Untuk anotasi `**@OneToMany**` , kita perlu menambahkan atribut `**mappedBy**` untuk menandakan ke *entity* mana relasinya akan dibuat.

Class Tour.java with relationship definition

Class TourPackage.java with relationship definition

*See you in the next part* 🔥