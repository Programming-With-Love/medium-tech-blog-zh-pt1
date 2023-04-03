# Factory Design Pattern Pada Golang

> 原文：<https://medium.easyread.co/factory-design-pattern-dengan-golang-dc683bfede0f?source=collection_archive---------0----------------------->

## Implementasi Factory Design Pattern Pada Golang

![](img/d03acb224f0665e9a8d1cecea73614fa.png)

**Factory Pattern** adalah salah satu *pattern* yang sangat populer dalam pemograman berorientasi objek. *Factory patter* n adalah bagian dari *creational design pattern* yang menyediakan solusi untuk pembuatan suatu *instance* atau objek.

Pada dasarnya, **Go** tidak mempunyai konsep *class* seperti bahasa pemograman OOP lainnya, tapi Go memiliki tipe struktur data yang disebut dengan **Struct** . Berikut contoh sebuah *struct* `**Mahasiswa**` beserta dengan method `**Greet**` :

Dari *struct* tersebut, kita bisa menginisialisasi dengan cara:

# Implementasi Factory Pattern

## Simple Factory

Cara paling simpel dan paling sering kita temui untuk *factory pattern* di Go adalah dengan membuat sebuah *function* yang menerima argumen dan mengembalikan sebuah *instance* . Berikut contohnya:

*Factory pattern* lebih baik digunakan karena bisa dipastikan ketika membuat *instance* dari *struct* tersebut, kita akan menginput semua atribut yang dibutuhkan. Dibandingkan dengan cara pertama, kita tidak diwajibkan untuk menginput semua atribut, misalnya kita bisa saja lupa memberikan parameter dan menginisialisasinya hanya dengan memanggil `m := Mahasiswa{}` .

Dan juga kelebihan lain dari menggunakan *factory pattern* adalah kita bisa menambahkan pengecekan/validasi pada *function factory* apakah argumen yang diberikan sesuai atau tidak.

> Gunakan factory pattern ketika dalam menginisiasi sebuah struct butuh satu atau beberapa argumen yang wajib.

## Interface Factory

*Factory pattern* juga dapat mengembalikan sebuah *interface.* Dengan *interface,* kita bisa mendefinisikan *behaviour* tanpa mengekspos implementasi internal. Artinya kita bisa membuat *structnya* menjadi *private* dan mendefinisikan method apa saja yang ingin kita ekspos di dalam *interface* .

Berikut contoh implementasinya:

## Multiple Implementations

Dengan menggunakan *interface* , kita bisa membuat beberapa *factory pattern* yang mengembalikan sebuah *interface* dan mengimplementasi *interface* tersebut dengan cara berbeda-beda.

Salah satu *usecase* yang sering digunakan adalah ketika membuat *mock* HTTP Client. Kita dapat membuah sebuah *interface* `**Doer**` yang diimplementasikan oleh Go standar HTPP Client, lalu kita membuat sebuah *function* *factory* yang mengembalikan *mock* HTTP Client. Berikut contohnya:

Karena `**NewHTTPClient**` dan `**NewMockHTTPClient**` keduanya mengembalikan tipe yang sama, maka keduanya bisa digunakan secara bergantian. Ini berguna ketika kita ingin membuat test menggunakan HTTP Client tanpa melakukan *request* yang sebenarnya.

# *Kesimpulan*

Kita telah melihat beberapa contoh menggunakan *factory pattern* dengan Go. Kesimpulan yang dapat diambil adalah:

*   Sembunyikan detail dalam pembuatan sebuah *instance* , dan berikan proses intantiasi ke *factory functio* n
*   *Design pattern* yang lain membutuhkan kita untuk membuat sebuah *struct* baru, sedangkan *factory pattern* hanya membutuhkan *function* baru.

Selamat mencoba!!!

# Referensi

[1] [https://refactoring.guru/design-patterns/factory-method](https://refactoring.guru/design-patterns/factory-method)

[2] [https://www.sohamkamani.com/golang/2018-06-20-golang-factory-patterns/](https://www.sohamkamani.com/golang/2018-06-20-golang-factory-patterns/)