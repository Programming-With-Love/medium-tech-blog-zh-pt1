# Cara Mengganti Nama Package di Android Studio

> 原文：<https://medium.easyread.co/cara-mengganti-nama-package-di-android-studio-dd7ae1f9fe8d?source=collection_archive---------0----------------------->

Cara Simpel mengubah nama package aplikasi

![](img/6965363b559cacf59429b21a16cf57e5.png)

Terkadang kita ingin sekali meng- *clone* project yang sudah jadi atau project orang lain *eh 🙈. Terus project tersebut mau dipakai untuk project yang lain. Tetapi kita seringkali kebingungan bagaimana caranya agar nama *package* project tersebut bisa diubah. *Nah* , sekarang tidak perlu bingung lagi. Kamu tinggal mengikuti jalan yang lurus, maksudnya *tips* dan *trick* di bawah ini. Check it out. *.*

## Langkah Pertama

*Copy* isi folder projectnya dan *paste* di folder lain dengan nama project yang baru.

## Langkah Kedua

Import Projectnya di Android Studio dengan File > New >Import Project. Pilih lokasi project. Klik icon gear dan klik “Compact Empty Middle Packages” untuk menghilangkan tanda centang

![](img/c6f75decbdef1b59f92a5f8bbe5af44a.png)

maka folder akan menjadi seperti ini.

![](img/5a00818ad7a37f8f5c4f91df9304e786.png)

## Langkah Ketiga

Kemudian rename folder package yang mau diganti dengan klik kanan > redactor > rename

![](img/d981fb15c8a192c344220faf5fd4bc7f.png)

pilih “Rename Package”

![](img/c1f1c974ae42897108149bf21ef6bf08.png)

ganti namanya > refactor.

![](img/3b8c63b57e8f334bfa87c4c4fb3c1b95.png)

lalu akan muncul peringatan di bawah. Pilih “Do refactor”, maka dia akan otomatis mengubah semua yang terhubung ke package itu.

![](img/1475c65cf66837e6f78635434abdc40b.png)

Silahkan lakukan hal yang sama jika anda ingin mengganti sub package yang lain.

## Langkah Terakhir

Jika sudah, jangan lupa mengganti juga *application id* di build.gradle (app)

![](img/00ecfee445313686710e34454b0d9ee7.png)

Done. Selesai.. Gampang kan? Tapi kalau gak ada yang kasitau emang susah 🙈 🙊

> Memang susah ketika melakukan PERUBAHAN, tapi akan Lebih Susah jika TIDAK SEGERA BERUBAH~