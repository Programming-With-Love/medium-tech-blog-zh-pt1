# Membuat Tampilan Web dengan Blade pada aplikasi Laravel — Part II

> 原文：<https://medium.easyread.co/membuat-tampilan-web-dengan-blade-pada-aplikasi-laravel-part-ii-9e233233972a?source=collection_archive---------2----------------------->

## Part 11 — View and Blade

![](img/dd7d9c40f0b971339199a885f5bee92f.png)

Photo by [Kaleidico](https://unsplash.com/@kaleidico?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# Laravel Series List

[**0\. Laravel Series — Belajar Laravel dari Awal yok!**](/easyread/laravel-series-belajar-laravel-dari-awal-yok-c21dc47863da)[**1\. Persiapan untuk Pengerjaan Proyek dengan Laravel**](/easyread/persiapan-untuk-pengerjaan-proyek-dengan-laravel-2f9a99146313)[**2\. Pengenalan Laravel Framework**](/easyread/pengenalan-laravel-framework-1c829b8164af)[**3\. Instalasi Laravel Framework**](/easyread/instalasi-laravel-framework-41eeec1551ef)[**4\. Struktur Folder Laravel Framework**](/easyread/struktur-folder-laravel-framework-299f0225cd55)[**5\. Apa itu Artisan CLI pada Laravel?**](/easyread/apa-itu-artisan-cli-pada-laravel-62a94232a29a)[**6\. Rancang Database-mu dengan Migration Pada Laravel**](/easyread/rancang-database-mu-dengan-migration-pada-laravel-28d419d0089e)[**7\. Mengarahkan Request dengan Router pada Laravel**](/easyread/mengarahkan-request-dengan-router-pada-laravel-a0df91142f51)[**8\. Olah Request dengan Controller pada Laravel**](/easyread/olah-request-dengan-controller-pada-laravel-a77b52235a4b)[**9\. Mudahnya Mengolah Data Menggunakan Model dan Eloquent pada Laravel**](/easyread/mudahnya-mengolah-data-menggunakan-model-dan-eloquent-pada-laravel-80af915c80b5)[**10\. Membuat Tampilan Web dengan Blade pada aplikasi Laravel — Part I**](https://medium.com/easyread/membuat-tampilan-web-dengan-blade-pada-aplikasi-laravel-part-i-c9f5ceee65e6) **11\. Membuat Tampilan Web dengan Blade pada aplikasi Laravel — Part II — (You’re here)**

Setelah belajar membuat tampilan dengan blade, ternyata masih ada bagian yang bisa kita perbaiki dari kode program tampilan web. Mari kita lihat kode program kita.

Kode program untuk tampilan daftar buku

Kode program untuk tampilan form penambahan buku

Kode program untuk tampilan detail buku

Kode program untuk tampilan form pengubahan data buku

Pada keempat kode program diatas, kita bisa melihat kode program yang berulang, yaitu

Bagian *title, header, content,* dan *footer* ditulis berulang kali. Lalu apa yang yang bisa kita lakukan untuk memperbaiki kode program kita? Kode program berulang ini dapat kita sederhanakan dengan menuliskannya hanya satu kali saja. Bagaimana caranya?

# Blade Template Engine

Pada part sebelumnya, kita sudah menggunakan fitur dari blade untuk memlakukan perulangan untuk menampilkan data dan menapilkan data dari sebuah variable. Ternyata, blade memiliki banyak fitur lainnya. Salah satunya adalah ***layout extension*** .

***Layout extension*** adalah fitur yang memungkinkan kita untuk menggunakan menggunakan kode program lain pada kode program lainnya. Contohnya adalah kita dapat menuliskan kode program untuk *footer* sekali saja dan menggunakannya disemua tampilan. Bagaimana caranya?

# Membuat Kerangka Tampilan

Kerangka tampilan yang akan kita buat akan mengikuti layout yang sudah kita tentukan, yaitu terdapat header, content, dan footer.

Kode program kerangka tampilan

Pada kode program diatas, kita bisa melihat bahwa kode program tersebut adalah kode program yang berulang kita tuliskan ditampilan kita. Kode program inilah yang akan menjadi kerangka, artinya kode program ini cukup dituliskan sekali saja, lalu bagian lainnya akan diisi oleh file lainnya.

*   `**@include**` adalah blade helper yang digunakan untuk mengikutikan kode program dari file lain. Helper ini akan menerima satu parameter, yaitu lokasi dari file yang akan dikutsertakan. Pada contoh diatas, paramter yang diberikan adalah `**layouts/header**` , artinya kita akan mengikutsertakan kode program dari file `**resources/views/layouts/header.blade.php**`
*   `**@yield**` adalah blade helper yang digunakan untuk menyediakan suatu tempat yang nantinya akan diisi oleh kode program dari file lain.

Kode program untuk bagian header akan kita pisahkan menjadi file baru yaitu `**header.blade.php**` . Berikut adalah kode programnya

Kode program untuk tampilan header

Begitu pula untuk kode program pada bagian footer akan kita pisahkan menjadi file baru yaitu `**footer.blade.php**` . Berikut adalah kode programnya

Kode program untuk tampilan footer

Untuk bagian *title* dan *content* -nya sendiri akan kita masukkan jadi file lain. Berikut adalah kode program untuk tampilan daftar buku.

Kode program untuk menampilkan data buku

Pada kode program diatas, kita menggunakan dua blade helper lain yaitu `**@extends**` dan `**@section**` . Berikut adalah penjelasannya.

*   `**@extends**` adalah blade helper yang kita gunakan untuk menggunakan kode program lain sebagai kerangka atau landasan. `**@extends**` menerima satu parameter yaitu lokasi dari file yang akan digunakan sebagai kerangka. Pada contoh ini, parameternya adalah `**layouts.app**` , artinya kode program yang akan digunakan adalah `**resources/views/layouts/app.blade.php**` . Dengan menggunakan kode program dari `**app.blade.php**` , kita dapat menggunakan `**@yield**` yang terdapat pada kode program tersebut.
*   `**@section**` adalah blade helper yang kita gunakan untuk mengisi `**@yield**` yang terdapat pada kode program yang di-extends sebelumnya. Pada `**app.blade.php**` , terdapat dua `**@yield**` yaitu `**@yield('title')**` dan `**@yield('content')**` . artinya terdapat dua buat tempat untuk kita mengisi dengan kode program yang lain. `**@yeild**` akan dipasangkan dengan `**@section**` . Pada contoh kode program diatas, kode program yang terdapat diantara `**@section('title')**` dan `**@endsection**` akan mengisi bagian pada `**@yield('title')**` dan bagitu juga pada `**@section('content')**` dan `**@endsection**` akan mengisi bagian pada `**@yield('content')**` .

Dengan menggunakan blade helper ini, kode program akan tersusun lebih rapi lagi. Kita dapat menyusun file-file berdasarkan kegunaannya masing-masing. Seperti contoh diatas, kita memisahkan file yang khusus menangani kerangka dan yang menangani konten dari.

Contoh lainnya adalah ketika kita ingin menambahkan sidebar pada semua tampilan, kita hanya perlu membuat satu file baru yaitu `**sidebar.blade.php**` dan kita tempatkan pada `**resources/views/layouts/sidebar.blade.php**` . Lalu file ini akan di include pada `**app.blade.php**` . Semua file yang meng-extends `**app.blade.php**` akan secara otomatis mendapatkan tampilan sidebar.

Masih banyak fitur blade lainnya yang belum kita pelajari pada artikel ini. Teman — teman dapat mempelajarinya pada dokumentasi laravel.

Oh ya, kode program yang sudah kita kerjakan selama series ini dapat diakses pada repositori berikut.

[](https://github.com/ecojuntak/library) [## ecojuntak/library

### Laravel is a web application framework with expressive, elegant syntax. We believe development must be an enjoyable and…

github.com](https://github.com/ecojuntak/library) ![](img/bbc25bc8dae544598ca47e1a85130bf9.png)

Taken from me.me

Cappy Hoding! ❤️ = ☕️ + 💻