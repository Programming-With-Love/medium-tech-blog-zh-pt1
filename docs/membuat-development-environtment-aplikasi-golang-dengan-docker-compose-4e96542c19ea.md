# Membuat Development Environment Aplikasi Golang dengan Docker Compose

> 原文：<https://medium.easyread.co/membuat-development-environtment-aplikasi-golang-dengan-docker-compose-4e96542c19ea?source=collection_archive---------2----------------------->

## Part 6 dari Project [Kube-Xmas Series](https://medium.com/easyread/christmas-tale-of-sofware-engineer-project-kube-xmas-9167ebca70d2)

![](img/7d6dba01403374b0bf0120a27beef3e5.png)

Photo by [rawpixel](https://unsplash.com/@rawpixel?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Haloo lagi, hehe. Di part ini saya akan sedikit membahas topik bagaimana mengatur environtment development dengan docker-compose.

Misalnya, kita lagi bekerja dalam tim. Ada banyak backend-engineer yang akan bersama-sama mengembangkan satu aplikasi. Langsung ke kasus nyata, project Kube-Xmas, saya memiliki aplikasi tweet API. Aplikasi ini pastinya memiliki external-dependencies, untuk kasus saya, external dependenciesnya adalah Redis.

Nah, normalnya, ketika mendevelop aplikasi seperti ini, setiap anggota tim backend pasti memiliki environtment berbeda-beda. Ada yang menggunakan Linux, MacOS, Windows dsb.

Belum lagi kalau butuh external-dep seperti redis, biasanya para engineer pasti akan meng-install aplikasi yang dibutuhkan di PC(laptop)nya masing-masing. Kalau butuh MySQL, maka setiap engineer biasanya meng-install MySQL di laptop mereka, jika butuh Mongo, sama mereka juga akan menginstall Mongo, dsb.

So, bagaimana, kalau semua hal itu kita ciptakan satu environtment yang sama untuk setiap development tanpa harus ribet ketika harus develop aplikasi yang akan dikerjakan.

Thanks to Docker-Compose, kita dapat dengan mudah melakukannya, meski tujuan awal dari Dokcer-Compose bukanlah hal ini hehe, tetapi kalau bisa dimanfaatin kenapa tidak hehe 😈

Jadi yang dibutuhkan adalah membuat dokcer-compose.yaml, yang nantinya akan digenerate oleh docker-compose. Untuk sampai ketahapan ini, ada baiknya anda telah men-dockerize aplikasi yang akan kita jalankan didalam docker-compose. Caranya bisa dilihat dipart sebelumnya :D

Berikut saya sudah buat docker-compose untuk aplikasi tweetor di Project Kube-Xmas Series saya.

`version` : Menyatakan *versi docker compose* yang saya pakai. Untuk menggunakan dokcer-compose harap menggunakan versi tercamtum disini, karena versi 3 keatas sudah menjadi docker-swarm yang cara kerjanya sedikit berbeda dengan docker-compose.
`services` : Daftar *service* yang saya jalankan di *docker compose* .
`image` : *Image* yang akan kita run di *docker-compose*
`ports` : Menyatakan port yang *diexpose* oleh *service* tersebut didalam satu jaringan *docker compose*
`depends_on` : Menyatakan suatu *service* bergantung pada *service* lain. Sehingga *service* tersebut akan dijalankan setelah *service* yang bergantung kepadanya sudah aktif.
`volumes` : Menyatakan file yang kita *inject* atau sisipkan kedalam *container* yang akan dijalankan didalam service *docker compose* . Bisa *config* file, atau apapun.

Selanjutnya dengan *command* simple:

```
$ docker-compose up -d
```

Maka *service* kita sudah aktif di lokal. Hal ini sangat membantu untuk kebutuhan *development* . Anda tidak perlu setup aneh-aneh di lokal anda. Biarkan *docker-compose* meng- *install* kebutuhan apa saja yang diperlukan oleh aplikasi anda.

Tidak penting OSnya apa, selama *docker* bisa berjalan, *experience* -nya tetap sama. *Environment development* -nya tetap sama. Aplikasi berjalan layaknya di Kubernetes (diatas container), jadi experiencenya tetap sama. Jika ada error, maka sudah pasti error ketika deploy ke kubernetes.

## Stop Service

Untuk *stop service* , kita dapat melakukannya dengan *command* :

```
$ docker-compose down
```

Maka semua service kita akan selesai. Kita juga dapat melihat logs dengan *command*

```
$ docker-compose logs -f
```

Namun *logs* yang terdapat didalamnya adalah logs semua aplikasi yang berjalan dalam satu network docker compose.

## Demo

Mempermudah penggunaanya, dapat dilihat dalam demo ini. :D

Yaayy!!! Another Post finished :D

Sampai disini, seharusnya jika anda menjalankan ini, ketika bekerja dalam tim, experience nya tetap sama. Untuk menjalankan aplikasinya kita tinggal menjalankan command `docker-compose up` di laptop masing-masing.

Nah, selanjutnya adalah, saya akan membuat Makefile yang berguna untuk otomoasi semua proses ini. Begitu banyak command dan arguments yang dibutuhkan, tentunya bisa membuat kita lupa. Nah biar tidak mudah lupa, serta membantu dalam otomasi (untuk kebutuhan CI/CD) saya pun membuat Makefile yang mempermudah semua command tersebut.

## Next

*   [**Membuat Makefile untuk aplikasi Golang**](https://medium.com/easyread/membuat-makefile-untuk-aplikasi-golang-5c2d19122b13)

## Prev

*   [**Men-dockerize Aplikasi Golang**](https://medium.com/easyread/men-dockerize-aplikasi-golang-9c32959c657e)