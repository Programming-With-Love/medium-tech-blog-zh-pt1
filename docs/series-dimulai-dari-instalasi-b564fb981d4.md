# Series — Dimulai dari Instalasi!

> 原文：<https://medium.easyread.co/series-dimulai-dari-instalasi-b564fb981d4?source=collection_archive---------0----------------------->

## REST API WITH SPRING BOOT

## 1 — Developing Tools

![](img/79fc6a10e62dff35259fc4e1655ebf4c.png)

Photo by [Fab Lenz](https://unsplash.com/@fossy) on [unsplash](https://unsplash.com/\)

# Rest API dengan Spring Boot — Series List

[**0\. Series — REST API dengan Spring Boot**](https://medium.com/easyread/series-rest-api-dengan-spring-boo-2d74060e69fb) **1\. Series — Dimulai dari Instalasi! — (You’re here)** [**2\. Series — Inisiasi Project**](https://medium.com/easyread/series-inisiasi-project-1e37ffa951ed)[**3\. Series — Rancangan dan Konfigurasi Database dengan H2**](https://medium.com/easyread/series-rancangan-dan-konfigurasi-database-dengan-h2-3af60e66e4ef)[**4\. Series — Membuat Data Model dengan Sentuhan Lombok**](https://medium.com/easyread/series-membuat-data-model-dengan-sentuhan-lombok-af4a57a75198)[**5\. Series — Menggunakan Spring JPA (1)**](https://medium.com/easyread/series-menggunakan-spring-jpa-1-da3ea1274f7d)[**6\. Series — Menggunakan Spring JPA (2)**](https://medium.com/easyread/series-menggunakan-spring-jpa-2-8673af359e1a)[**7\. Series — Inisialisasi Data**](https://medium.com/easyread/series-inisialisasi-data-aa2ae7d36691)

Sebelum kita memulai mengerjakan *project* ini, ada baiknya kita mempersiapkan beberapa hal terlebih dahulu.

## Java

Sepanjang *series* ini, saya menggunakan java 1.8\. Pastikan teman-teman semua sudah *install* java di *local* dengan menggunakan *command* :

```
**java -version**
```

## Maven

*Maven* adalah *Java Build Tools* yang menggunakan konsep *Project Object Model* (POM). POM tersebut berisi informasi dan konfigurasi yang digunakan Maven untuk *generate project* . Pada dasarnya POM adalah sebuah XML *file* yang terdapat di dalam *project* Maven dan di dalam *file* inilah konfigurasi dari *project* kita berada. *Simple-* nya, jika PHP punya *composer* , maka *java* punya *maven* . Saya menggunakan *maven* 3.6.0 dalam *project* ini. Pastikan *maven* juga sudah ter- *install* di *local* teman — teman dengan *command* :

```
**mvn -version**
```

## Code Editor

*Code editor* adalah salah satu *tools* yang kita butuhkan untuk mengetikkan *code* kita nantinya. Ada sangat banyak pilihan untuk *code editor* ini, saya akan menggunakan **Intellij IDEA** . Teman-teman bebas milih kok, senyamannya aja.

## Git

*Git* adalah salah satu *version control* yang bertugas mencatat setiap perubahan pada *file* proyek yang kita kerjakan. *Git* ini juga memungkinkan kita untuk melakukan kolaborasi dalam suatu *project* . Untuk memastikan teman-teman sudah *install* *git* nya dengan *command* :

```
**git --version**
```

Bagi yang belum familiar dengan Git, mungkin dapat membaca tulisan berikut [**Belajar Git Dalam Waktu Singkat (Studi Kasus: Github)**](https://medium.com/easyread/belajar-git-dari-dasar-dalam-waktu-singkat-github-82e5e7880b4e) oleh [Sogumontar Hendra Simangunsong](https://medium.com/u/d6d74fde68be?source=post_page-----b564fb981d4--------------------------------) .

## Postman

*Postman* adalah sebuah *tools* yang berfungsi sebagai *REST client* yang akan kita gunakan untuk menguji *REST API* yang kita *develop* . Saya menggunakan *postman* karena aplikasi ini *awesome* dan memudahkan proses *testing* dengan *user experience* yang baik. *Postman* juga memiliki fitur *Sharing Collection API* yang menurut saya sangat membantu dan masih ada banyak fitur lain, silahkan teman-teman cek sendiri di [sini](https://www.postman.com/) .

*See you in the next part* 🔥