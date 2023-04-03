# Persiapan untuk Pengerjaan Proyek dengan Laravel

> 原文：<https://medium.easyread.co/persiapan-untuk-pengerjaan-proyek-dengan-laravel-2f9a99146313?source=collection_archive---------0----------------------->

## Part 1 — Preparation

![](img/0818334d5505ac84f0533cf9cc139db5.png)

Photo by [rawpixel](https://unsplash.com/@rawpixel?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## **Laravel Series List**

[**0\. Laravel Series — Belajar Laravel dari Awal yok!**](https://medium.com/easyread/laravel-series-belajar-laravel-dari-awal-yok-c21dc47863da) **1\. Persiapan untuk Pengerjaan Proyek dengan Laravel — (You’re here)** [**2\. Pengenalan Laravel Framework**](https://medium.com/easyread/pengenalan-laravel-framework-1c829b8164af)[**3\. Instalasi Laravel Framework**](https://medium.com/easyread/instalasi-laravel-framework-41eeec1551ef)[**4\. Struktur Folder Laravel Framework**](/easyread/struktur-folder-laravel-framework-299f0225cd55)[**5\. Apa itu Artisan CLI pada Laravel?**](https://medium.com/easyread/apa-itu-artisan-cli-pada-laravel-62a94232a29a)[**6\. Rancang Database-mu dengan Migration Pada Laravel**](https://medium.com/easyread/rancang-database-mu-dengan-migration-pada-laravel-28d419d0089e)[**7\. Mengarahkan Request dengan Router pada Laravel**](https://medium.com/easyread/mengarahkan-request-dengan-router-pada-laravel-a0df91142f51)[**8\. Olah Request dengan Controller pada Laravel**](https://medium.com/easyread/olah-request-dengan-controller-pada-laravel-a77b52235a4b)[**9\. Mudahnya Mengolah Data Menggunakan Model dan Eloquent pada Laravel**](https://medium.com/easyread/mudahnya-mengolah-data-menggunakan-model-dan-eloquent-pada-laravel-80af915c80b5)[**10\. Membuat Tampilan Web dengan Blade pada aplikasi Laravel — Part I**](https://medium.com/easyread/membuat-tampilan-web-dengan-blade-pada-aplikasi-laravel-part-i-c9f5ceee65e6)[**11\. Membuat Tampilan Web dengan Blade pada aplikasi Laravel — Part II**](https://medium.com/easyread/membuat-tampilan-web-dengan-blade-pada-aplikasi-laravel-part-ii-9e233233972a)

Untuk memulai pembuatan aplikasi berbasis website dengan Laravel Framework, kita perlu meng- *install* beberapa *tools* untuk membantu pengerjaan kita.

# Bahasa Pemrograman PHP

Laravel Framework adalah kerangka kerja pembangunan aplikasi berbasis *website* yang dikembangkan dengan bahasa pemrograman PHP. Karena itu, kita wajib untuk meng- *install* bahasa pemrograman PHP. Untuk pemasangan PHP, terdapat cara yang berbeda-beda untuk setiap sistem operasi.

Sepanjang series ini, saya akan menggunakan Laravel versi 5.7\. Untuk menggunakan Laravel versi 5.7, versi PHP yang dibutuhkan adalah minimal PHP 7.1.3\. Jadi jangan sampai salah meng- *install.* Untuk memastikan PHP sudah ter- *install* dengan benar, jalankan perintah ini di Terminal/CMD.

```
**$ php --version**
```

# MySQL

Sepanjang series ini, *database* yang akan saya gunakan adalah MySQL. MySQL salah satu *database* yang paling terkenal di kalangan pengembang pemula, terutama yang masih duduk di kursi perkuliahan. Untuk versi yang akan saya gunakan adalah versi 5.7.25\. Akan ada perbedaan cara pemasangan MySQL *database* untuk sistem operasi yang berbeda. Untuk memastikan MySQL sudah ter- *install* dengan benar, jalankan perintah ini pada Terminal/CMD.

```
**$ mysql --version**
```

# Composer

Composer adalah *tool* yang berguna untuk mengolah *library* yang akan digunakan oleh Laravel. Ingat, Laravel juga menggunakan beberapa *library* yang dikembangkan oleh pengembang lain. Akan ada perbedaan cara pemasangan Composer untuk sistem operasi yang berbeda. Untuk memastikan Composer sudah ter- *install* dengan benar, jalankan perintah ini pada Terminal/CMD.

```
**$ composer --version**
```

Sebenarnya cukup dengan ketiga *tools* ini, kita sudah bisa mengembangkan aplikasi website kita dengan Laravel. Tapi, ada beberapa *tools* yang saya rasa masih perlu dan teman-teman bisa pakai.

# Code Editor

*Code editor* adalah tempat kita akan mengetikkan kode program kita. Pilihan untuk *code editor* sebenarnya sangat banyak. Teman-teman bebas memilih menggunakan Sublime Text, VSCode, PHPStorm, atau *code editor* lain. Kalau saya sendiri sudah terbiasa dan senang menggunakan PHPStrom karena fitur yang diberikan cukup lengkap.

PHPStrom adalah IDE yang dikembangkan oleh [JetBrains](https://www.jetbrains.com/) . Untuk menggunakan PHPStrom, diberikan 30 hari penggunaan secara gratis, selebihnya diperlukan *license key.* Untuk teman-teman mahasiswa yang ingin menggunakan PHPStrom jangan takut dulu. JetBrains menyediakan JetBrains Student Pack, dimana teman-teman bisa mendapatkan *license key* secara gratis untuk satu tahun penuh dan dapat diperpanjang untuk tahun berikutnya. Syarat untuk mendapat *license key* gratis adalah dengan mendaftar dengan alamat email kampus teman-teman. Satu *license key* dapat digunakan untuk semua produk JetBrains. * *Maaf, bukan mau promosi* 😆*

# Database Client Tools

Database client tools adalah *tools* yang akan kita gunakan untuk menampilkan data yang akan kita olah pada *database* kita. Untuk MySQL *database, database client tools* yang paling umum digunakan adalah PHPMyAdmin. Tetapi teman-teman juga bisa menggunakan SQLyog atau yang lainnya. Kalau saya sendiri menggunakan DataGrip karena dapat digunakan untuk mengolah banyak jenis *database.* DataGrip juga termasuk salah satu produknya JetBrains.

![](img/c258887b5d2f41a858bdb602bbaa2a9a.png)

Taken from me.me

Sampai jumpa di- *part* berikutnya!

Cappy Hoding! ❤️ = ☕️ + 💻