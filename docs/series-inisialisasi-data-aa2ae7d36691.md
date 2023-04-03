# Series — Inisialisasi Data

> 原文：<https://medium.easyread.co/series-inisialisasi-data-aa2ae7d36691?source=collection_archive---------2----------------------->

## 6\. Mapping JSON Data

![](img/eaff09b52ed9a30445aee7047df0cbe6.png)

Photo by [Isaac Smith](https://unsplash.com/@isaacmsmith?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/data?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# Rest API dengan Spring Boot — Series List

[**0\. Series — REST API dengan Spring Boot**](https://medium.com/easyread/series-rest-api-dengan-spring-boo-2d74060e69fb)[**1\. Series — Dimulai dari Instalasi!**](https://medium.com/easyread/series-dimulai-dari-instalasi-b564fb981d4)[**2\. Series — Inisiasi Project**](https://medium.com/easyread/series-inisiasi-project-1e37ffa951ed)[**3\. Series — Rancangan dan Konfigurasi Database dengan H2**](https://medium.com/easyread/series-rancangan-dan-konfigurasi-database-dengan-h2-3af60e66e4ef)[**4\. Series — Membuat Data Model dengan Sentuhan Lombok**](https://medium.com/easyread/series-membuat-data-model-dengan-sentuhan-lombok-af4a57a75198)[**5\. Series — Menggunakan Spring JPA (1)**](https://medium.com/easyread/series-menggunakan-spring-jpa-1-da3ea1274f7d)[**6\. Series — Menggunakan Spring JPA (2)**](https://medium.com/easyread/series-menggunakan-spring-jpa-2-8673af359e1a) **7\. Series — Inisialisasi Data — You’re here**

Pada *part* kali ini kita akan mencoba untuk membuat *predefined* data untuk *database* kita. Karena kita menggunakan H2 *database* , kita perlu membuat *initial* data yang akan dieksekusi setiap kali aplikasi dijalankan. Pertama, kita akan membuat sebuah *file* *json* yang akan berisi *initial* data kita. Mari kita buat *file* `**TourData.json**` di dalam *package* **resources** .

Setelah itu, kita akan membuat sebuah *helper* *class* yang akan kita gunakan untuk meng- *import* data dari `**TourData.json**` kita ke dalam *database* kita. Pertama kita buat *package* `**helper**` dan kita buat sebuah *class* dengan nama `**TourFromFile.java**` dan akan kita isi dengan sebuah *function* yang akan melakukan *mapping* dari *file* json menjadi sebuah *object* .

Nah, berikutnya mari kita buka *class* `**SpringBootRestApiApplication**` dan kita akan *implement* sebuah *interface* yang bernama `**CommandLineRunner**` **.** *Interface* ini adalah *interface* yang ada pada *spring boot* yang memiliki sebuah *run* *method* . Secara otomatis, *spring boot* sendiri akan menjalankan *method* ini ketika aplikasinya kita jalankan. Setelah kita *implement* *interface* tersebut, berikutnya kita akan *inject* *service* yang telah kita buat agar dapat kita gunakan untuk *insert* *initial* data kita.

Setelah semua kita siapkan, *we’re good to go* . Teman — teman sekarang boleh coba *run* *project* -nya dan cek databasenya maka akan ada 9 data pada *table* **tour package** dan 5 data pada *table* **tour** .

See you in the next part 🔥