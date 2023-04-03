# Instalasi Laravel Framework

> 原文：<https://medium.easyread.co/instalasi-laravel-framework-41eeec1551ef?source=collection_archive---------0----------------------->

## Part 3 — Installation

![](img/7c82787c4ec4d3e7e8f1f4f2d53abe0a.png)

Photo by [Markus Spiske](https://unsplash.com/@markusspiske?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# Laravel Series List

[**0\. Laravel Series — Belajar Laravel dari Awal yok!**](https://medium.com/easyread/laravel-series-belajar-laravel-dari-awal-yok-c21dc47863da)[**1\. Persiapan untuk Pengerjaan Proyek dengan Laravel**](https://medium.com/easyread/persiapan-untuk-pengerjaan-proyek-dengan-laravel-2f9a99146313)[**2\. Pengenalan Laravel Framework**](https://medium.com/easyread/pengenalan-laravel-framework-1c829b8164af) **3\. Instalasi Laravel Framework — (You’re here)** [**4\. Struktur Folder Laravel Framework**](https://medium.com/easyread/struktur-folder-laravel-framework-299f0225cd55)[**5\. Apa itu Artisan CLI pada Laravel?**](https://medium.com/easyread/apa-itu-artisan-cli-pada-laravel-62a94232a29a)[**6\. Rancang Database-mu dengan Migration Pada Laravel**](https://medium.com/easyread/rancang-database-mu-dengan-migration-pada-laravel-28d419d0089e)[**7\. Mengarahkan Request dengan Router pada Laravel**](https://medium.com/easyread/mengarahkan-request-dengan-router-pada-laravel-a0df91142f51)[**8\. Olah Request dengan Controller pada Laravel**](https://medium.com/easyread/olah-request-dengan-controller-pada-laravel-a77b52235a4b)[**9\. Mudahnya Mengolah Data Menggunakan Model dan Eloquent pada Laravel**](https://medium.com/easyread/mudahnya-mengolah-data-menggunakan-model-dan-eloquent-pada-laravel-80af915c80b5)[**10\. Membuat Tampilan Web dengan Blade pada aplikasi Laravel — Part I**](https://medium.com/easyread/membuat-tampilan-web-dengan-blade-pada-aplikasi-laravel-part-i-c9f5ceee65e6)[**11\. Membuat Tampilan Web dengan Blade pada aplikasi Laravel — Part II**](https://medium.com/easyread/membuat-tampilan-web-dengan-blade-pada-aplikasi-laravel-part-ii-9e233233972a)

Untuk meng- *install* Laravel Framework, terdapat beberapa cara yang dapat kita gunakan, yaitu dengan **Laravel Installer** dan **Composer Create-Project.**

# Via Laravel Installer

Untuk menggunakan ***Laravel Installer*** , kita perlu untuk mengunduh *installer* -nya pertama sekali. Untuk mengunduh *installer* -nya, jalankan perintah berikut pada Terminal/CMD.

```
**$ composer global require laravel/installer**
```

Pastikan komputer anda terkoneksi dengan jaringan internet. Dengan mengunduh Laravel Installer untuk membuat *project* Laravel baru, anda tidak perlu lagi terkoneksi dengan internet, kecuali terdapat beberapa *update* -an. Setelah sukses mengunduh Laravel Installer-nya, sekarang kita akan membuat *project* Laravel yang baru dengan menjalankan perintah berikut.

```
**$ laravel new blog**
```

Mari kita bedah perintah diatas

*   `**laravel**` adalah perintah untuk memanggil Laravel Installer
*   `**new**` adalah perintah untuk menandakan bahwa kita ingin membuat *project* baru
*   `**blog**` adalah nama dari *project* yang akan kita buat. Jadi bisa digantikan dengan nama aplikasi yang akan teman-teman buat.

# **Via Composer Create-Project**

Untuk menggunakan ***Composer Create-Project*** , kita tinggal menjalankan perintah dibawah ini.

```
**$ composer create-project --prefer-dist laravel/laravel blog**
```

Mari kita bedah perintah diatas

*   `**composer**` adalah perintah untuk menjalankan Composer
*   `**create-project**` adalah perintah untuk membuat *project* dari *library* yang sudah ada
*   `**--prefer-dist**` adalah *flag* untuk mengutamakan peng- *install* -an melalui folder `dist` jika ada
*   `**laravel/laravel**` adalah nama dari *package* yang akan digunakan sebagai sumber kode program
*   `**blog**` adalah nama dari *project* yang akan kita buat. Jadi bisa digantikan dengan nama aplikasi yang akan teman-teman buat.

Untuk meng- *install* Laravel dengan versi yang lebih spesifik, bisa dilakukan dengan perintah dibawah ini.

```
**$ composer create-project --prefer-dist laravel/laravel blog "5.8.*"**
```

Contoh diatas adalah perintah untuk meng- *install* Laravel versi **5.8.*.** Tanda * pada versi diatas menandakan bahwa kita akan menggunakan versi *patch* paling terakhir pada versi 5.8\. Jika belum paham mengenai *semantic versioning* , teman-teman dapat membaca pada [link](https://semver.org/) berikut.

Pada perintah diatas, kita menambahkan versi Laravel sesuai dengan yang kita inginkan. Hal yang sama berlaku juga untuk versi yang lain.

![](img/c258887b5d2f41a858bdb602bbaa2a9a.png)

Taken from me.me

Sampai jumpa di- *part* berikutnya!

Cappy Hoding! ❤️ = ☕️ + 💻