# Pengenalan Laravel Framework

> 原文：<https://medium.easyread.co/pengenalan-laravel-framework-1c829b8164af?source=collection_archive---------1----------------------->

## Part 2 — Introduction

![](img/ee295d83720ae67fe4cc9e1b8f1ba75e.png)

Photo by [Vladislav Klapin](https://unsplash.com/@lemonvlad?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

## Laravel Series List

[**0\. Laravel Series — Belajar Laravel dari Awal yok!**](https://medium.com/easyread/laravel-series-belajar-laravel-dari-awal-yok-c21dc47863da)[**1\. Persiapan untuk Pengerjaan Proyek dengan Laravel**](https://medium.com/easyread/persiapan-untuk-pengerjaan-proyek-dengan-laravel-2f9a99146313) **2\. Pengenalan Laravel Framework — (You’re here)** [**3\. Instalasi Laravel Framework**](https://medium.com/easyread/instalasi-laravel-framework-41eeec1551ef)[**4\. Struktur Folder Laravel Framework**](/easyread/struktur-folder-laravel-framework-299f0225cd55)[**5\. Apa itu Artisan CLI pada Laravel?**](https://medium.com/easyread/apa-itu-artisan-cli-pada-laravel-62a94232a29a)[**6\. Rancang Database-mu dengan Migration Pada Laravel**](https://medium.com/easyread/rancang-database-mu-dengan-migration-pada-laravel-28d419d0089e)[**7\. Mengarahkan Request dengan Router pada Laravel**](https://medium.com/easyread/mengarahkan-request-dengan-router-pada-laravel-a0df91142f51)[**8\. Olah Request dengan Controller pada Laravel**](https://medium.com/easyread/olah-request-dengan-controller-pada-laravel-a77b52235a4b)[**9\. Mudahnya Mengolah Data Menggunakan Model dan Eloquent pada Laravel**](https://medium.com/easyread/mudahnya-mengolah-data-menggunakan-model-dan-eloquent-pada-laravel-80af915c80b5)[**10\. Membuat Tampilan Web dengan Blade pada aplikasi Laravel — Part I**](https://medium.com/easyread/membuat-tampilan-web-dengan-blade-pada-aplikasi-laravel-part-i-c9f5ceee65e6)[**11\. Membuat Tampilan Web dengan Blade pada aplikasi Laravel — Part II**](https://medium.com/easyread/membuat-tampilan-web-dengan-blade-pada-aplikasi-laravel-part-ii-9e233233972a)

Laravel Framework adalah salah satu dari *framework* berbasis bahasa pemrograman PHP yang sangat populer sekarang ini. Pada dasarnya, Laravel digunakan untuk pengembangan aplikasi berbasis web. Laravel juga bisa digunakan untuk pengembangan REST API *service* . Tapi, kali ini saya akan membahas penggunaan Laravel untuk pengembangan website.

Pada aplikasi berbasis *website* umumnya terdapat tiga pembagian tugas besar, yaitu pengolahan tampilan, pengolahan data, dan pengolahan bisnis proses/logika kerja. Laravel mengadopsi pola pengembangan Model-View-Controller (MVC). Arsitektur MVC bertujuan untuk memenuhi *single responsibility principle* (SRP) *,* yaitu prinsip pemisahan komponen berdasarkan tugasnya masing-masing. Dengan mengadopsi pola pengembangan MVC, Laravel dapat membagi ketiga tugas yang ada pada aplikasi kita. Berikut adalah komponen MVC dan pembagian tugasnya.

*   Komponen Model bertugas untuk menangani pengolahan data.
*   Komponen View bertugas untuk menangani pengolahan tampilan kepada pengguna.
*   Komponen Controller bertugas untuk menangani pengerjaan bisnis proses.

![](img/b40da90850ee27c995531a42b4fbd4aa.png)

Taken from selftaughtcoders.com

Tapi dalam praktiknya, pola pengembangan MVC pada Laravel didukung oleh satu komponen yang tidak kalah penting, yaitu Router. Seperti namanya, Router akan bertugas untuk mengarahkan *request* yang kita kirimkan melalui *browser* . Saya tidak akan bahas bagaimana keempat komponen bekerja sama pada *part* ini, saya akan bahas mendalam pada *part* berikutnya. Yang terpenting adalah pemahaman kita akan tugas dari keempat komponen tersebut.

![](img/c258887b5d2f41a858bdb602bbaa2a9a.png)

Taken from me.me

Sampai jumpa di- *part* berikutnya!

Cappy Hoding! ❤️ = ☕️ + 💻