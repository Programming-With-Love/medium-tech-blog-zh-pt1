# Mudahnya Mengolah Data Menggunakan Model dan Eloquent pada Laravel

> 原文：<https://medium.easyread.co/mudahnya-mengolah-data-menggunakan-model-dan-eloquent-pada-laravel-80af915c80b5?source=collection_archive---------0----------------------->

## **Part 9 — Model and Eloquent**

![](img/59c5c76f9643a95925a3eafaddb90bc1.png)

`Photo by [Jan Kolar (www.kolar.io)](https://unsplash.com/@jankolar?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

# Laravel Series List

[**0\. Laravel Series — Belajar Laravel dari Awal yok!**](/easyread/laravel-series-belajar-laravel-dari-awal-yok-c21dc47863da)[**1\. Persiapan untuk Pengerjaan Proyek dengan Laravel**](/easyread/persiapan-untuk-pengerjaan-proyek-dengan-laravel-2f9a99146313)[**2\. Pengenalan Laravel Framework**](/easyread/pengenalan-laravel-framework-1c829b8164af)[**3\. Instalasi Laravel Framework**](/easyread/instalasi-laravel-framework-41eeec1551ef)[**4\. Struktur Folder Laravel Framework**](/easyread/struktur-folder-laravel-framework-299f0225cd55)[**5\. Apa itu Artisan CLI pada Laravel?**](/easyread/apa-itu-artisan-cli-pada-laravel-62a94232a29a)[**6\. Rancang Database-mu dengan Migration Pada Laravel**](/easyread/rancang-database-mu-dengan-migration-pada-laravel-28d419d0089e)[**7\. Mengarahkan Request dengan Router pada Laravel**](/easyread/mengarahkan-request-dengan-router-pada-laravel-a0df91142f51)[**8\. Olah Request dengan Controller pada Laravel**](https://medium.com/easyread/olah-request-dengan-controller-pada-laravel-a77b52235a4b) **9\. Mudahnya Mengolah Data Menggunakan Model dan Eloquent pada Laravel — (You’re here)** [**10\. Membuat Tampilan Web dengan Blade pada aplikasi Laravel — Part I**](https://medium.com/easyread/membuat-tampilan-web-dengan-blade-pada-aplikasi-laravel-part-i-c9f5ceee65e6)[**11\. Membuat Tampilan Web dengan Blade pada aplikasi Laravel — Part II**](https://medium.com/easyread/membuat-tampilan-web-dengan-blade-pada-aplikasi-laravel-part-ii-9e233233972a)

Setelah mempelajari bagaimana mengolah request dengan controller, maka selanjutnya kita akan mempelajari bagaimana mengolah data menggunakan model dan eloquent. Sebelum kita lanjutkan ke materi, mari kita lihat kembali gambar dibawah ini.

![](img/b40da90850ee27c995531a42b4fbd4aa.png)

Taken from selftaughtcoders.com

Jika kita perhatikan dari gambar diatas, controller akan menggunakan model untuk berhubungan ke database. Model akan berperan untuk menyimpan, mengambil, mengubah, dan menghapus data pada database. Lalu bagaimana kode program untuk mengimplementasikan model?

# Membuat File Model

Pertama sekali, kita akan men- *generate* file model kita menggunakan Artisan CLI. Untuk membuat file *model* menggunakan Artisan CLI, jalankan perintah berikut

```
**$ php artisan make:model Book**
```

Dengan menjalan perintah diatas, *file* model kita akan ter *generate* secara otomatis. Sebelumnya kita lanjut, mari kita bedah perintah diatas.

*   `**Book**` adalah parameter yang kita berikan sebagai nama dari file *model* yang akan kita buat.

Berikut adalah *file* model yang sudah ter- *generate*

Bisa kita perhatikan bahwa kelas `**Book**` adalah turunan dari kelas `**Model**` . Kelas `**Model**` sendiri adalah bagian dari *library* `**Eloquent**` , yaitu *library* yang membantu kita mengolah data pada aplikasi Laravel. Eloquent sudah menyediakan fitur-fitur yang luar biasa untuk mempermudah pekerjaan kita menggunakan Laravel. Jika teman-teman berpikir akan menuliskan kode program SQL untuk proses pengolahan data, maka teman-teman salah. Kita tidak perlu lagi menuliskan kode program SQL untuk mengolah data, melainkan Eloquent yang akan menangani itu semua. Bagaimana bisa?

# Menggunakan Model Pada Controller

Setelah model Book ter *generate* , sekarang kita akan menggunakan model Book pada contoller. Kita akan mengubah kode program pada controller kita sesuai dengan tugasnya masing-masing, seperti untuk mengambil semua data buku, mengambil satu data buku, menambah data buku, mengubah data buku, dan menghapus data buku.

## Mengambil Semua Data

Fungsi untuk ngambil semua data buku

Potongan kode diatas adalah fungsi untuk mengambil semua data buku. Mari kita bedah kode diatas.

*   `**$book = Book::all()**` adalah potongan kode untuk melakukan pengambilan data buku dari database. Fungsi `**all**` pada model sama dengan melakukan eksekusi kode SQL `**select * from books**` . Luar biasa bukan? Tanpa sintaks SQL kita dapat melakukan query SQL dengan mudah.
*   `**return $books**` adalah potongan kode untuk mengembalikan semua data buku.

## Mengambil Satu Data Buku

Fungsi untuk mengambil satu data buku

Potongan kode diatas adalah fungsi untuk mengambil satu data buku sesuai dengan ID buku yang diberikan. Mari kita bedah kode diatas.

*   `**public function show($id)**` adalah pendeklarasian fungsi. Fungsi ini menerima satu parameter yaitu `**$id**` . Nilai dari parameter `**$id**` akan ditentukan dari nilai yang diberikan pada router ketika pemanggilan URL. Untuk lebih jelasnya mari kita perhatikan potongan kode router dan controller kita. Berikut adalah router yang kita gunakan untuk mengambil satu data buku.

```
**Route::*get*('/books/{id}', 'BookController@show');**
```

Pada router, kita menentukan akan menerima satu parameter yaitu `**{id}**` . Nilai dari paramater `**{id}**` ini akan menjadi nilai dari parameter `**$id**` pada `**public function show($id)**` .

*   `**$book = Book::find($id)**` adalah potongan kode untuk mengambil satu data buku sesuai ID yang diberikan. Fungsi `**find($id)**` pada model sama dengan melakukan eksekusi kode SQL `**select * from books where id=?**` .
*   `**return $book**` adalah potongan kode untuk mengembalikan data buku.

## Menambah Data Buku

Fungsi untuk menambah data buku

Potongan kode diatas adalah fungsi untuk menambah data buku. Mari kita bedah kode diatas.

*   `**public function store(Request $request)**` adalah pendeklarasian fungsi. Fungsi ini akan menerima satu parameter yaitu `**$request**` . Nilai `**$request**` adalah informasi semua informasi mengenai request tersebut, seperti header, body, query paramter, dan lain-lain. Untuk `**$request**` kita tidak perlu menentukannya pada router. Terkhusus untuk `**$request**` , Laravel akan menangani itu untuk kita.
*   `**$book = new Book();**` adalah potongan kode untuk menciptakan object buku baru.
*   `**$book->title = $request->title**` adalah potongan kode untuk menset title buku yang diambil dari *request body* . Hal yang sama akan berlaku untuk author, publication, dan year.
*   `**$book->save()**` adalah potongan kode untuk menyimpan data buku ke database. Fungsi `**save()**` pada model sama dengan melakukan eksekusi kode SQL `**insert into books (title, author, publication, year, created_at, updated_at) values (Laravel Series, Eko, Easyread, 2019, 01/01/2020 19:12:12, 01/01/2020 19:12:12)**` . Semakin mudah bukan? Oh ya, mungkin teman-teman bingung dimana kita menset nilai dari `**created_at**` dan `**updated_at**` nya. Kita tidak perlu mensetnya, melainkan Laravel akan menangani itu untuk kita.

# Mengubah Data Buku

Fungsi untuk mengubah data buku

Potongan kode diatas adalah fungsi untuk mengubah data buku. Sekilas potongan kode diatas mirip dengan fungsi untuk menambah data buku. Mari kita bedah.

*   `**public function update(Request $request, $id)**` adalah pendeklarasian fungsi. Fungsi ini akan menerima dua parameter yaitu `**$request**` dan `**$id**` . `**$request**` akan menampung informasi mengenai request tersebut, termasuk dengan data baru yang akan dipakai untuk menggantikan data lama. `**$id**` akan menampung informasi ID buku yang akan diperbaharui.
*   `**$book = Book::find($id)**` adalah potongan kode untuk mengambil data buku sesuai dengan ID yang diberikan.
*   `**$book->title = $request->title**` adalah potongan kode untuk menset nilai terbaru untuk title. Hal ini berlaku untuk author, publication, dan year. Untuk nilai dari `**created_at**` tidak akan berubah sedangkan nilai dari `updated_at` akan ditangani oleh Laravel.
*   `**$book->update()**` adalah fungsi untuk menyimpan perubahan data pada database. Fungsi `**update()**` sama dengan mengeksekusi sintaks SQL `**update books set title = Tutorial Laravel, author = Simanjuntak, publication = Medium, year = 2020, created_at = 01/01/2020 19:12:12, updated_at = 23/01/2020 14:12:44 where id = 1**`

# Menghapus Data Buku

Fungsi untuk menghapus data buku

Potongan kode diatas adalah untuk menghapus data buku. Mari kita bedah kode diatas.

*   `**public function destroy($id)**` adalah pendeklarasian fungsi. Fungsi ini akan merima satu parameter yaitu `**$id**` dari buku yang akan dihapus. Seperti fungsi lainnya, nilai dari `**$id**` akan ditentukan dari nilai yang kita berikan ketika pemanggilan URL.
*   `**Book::find($id)**` adalah kode untuk mengambil data buku sesuai dengan ID yang diberikan.
*   `**$book->delete()**` adalah kode untuk menghapus data buku. Fungsi `**delete()**` sama dengan melakukan eksekusi kode program `**delete from books where id = 1**` .

Begitulah cara kita mengolah data pada aplikasi Laravel menggunakan Eloquent. Masih banyak fitur eloquent yang dapat digunakan. Teman-teman bisa mencoba eksplorasi sendiri.

Sampai jumpa di- *part* berikutnya!

![](img/c258887b5d2f41a858bdb602bbaa2a9a.png)

Taken from me.me

Cappy Hoding! ❤️ = ☕️ + 💻