# [Part 4] — Mengenal Routing Laravel 8 | Part 2

> 原文：<https://medium.easyread.co/part-4-mengenal-routing-laravel-8-part-2-a31157fd0e70?source=collection_archive---------1----------------------->

![](img/c9055432f0f979304d263c9c3fd04aee.png)

Photo by [adrian](https://unsplash.com/@aows?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Halo semua, melanjutkan tulisan sebelumnya yang sempat terpotong mengenai **[Part 4] — Mengenal Routing Laravel 8 | Part 1** . Berikut terusannya.

# Name Routes

**Named routes** memungkinkan pembuatan URL atau pengalihan yang nyaman untuk route tertentu. Kamu dapat menentukan nama untuk route dengan merangkai metode nama ke definisi route:

Bentuk codenya seperti ini,

```
Route::get('/user/profile', function () {
    //
})->name('profile');
```

# Route Groups

Grup rute memungkinkan kamu untuk berbagi atribut rute, seperti middleware, di sejumlah besar rute tanpa perlu menentukan atribut tersebut di setiap rute.

Grup bertingkat mencoba secara cerdas “menggabungkan” atribut dengan grup induknya. Middleware dan di mana kondisi digabungkan sementara nama dan prefiks ditambahkan. Pembatas ruang nama dan garis miring di awalan URI otomatis ditambahkan jika sesuai.

```
Route::group(....)
```

# Middleware

Untuk menetapkan middleware ke semua rute dalam grup, kamu dapat menggunakan metode middleware sebelum menentukan grup. Middleware dijalankan dalam urutan seperti yang tercantum dalam array:

```
Route::middleware(['first', 'second'])->group(function () {
    Route::get('/', function () {
        // Uses first & second middleware...
    });

    Route::get('/user/profile', function () {
        // Uses first & second middleware...
    });
});
```

# Prefixes

Metode prefiks dapat digunakan untuk mengawali setiap rute dalam grup dengan URI tertentu. Misalnya, kamu mungkin ingin memberi awalan semua URI rute dalam grup dengan admin:

```
Route::prefix('admin')->group(function () {
    Route::get('/users', function () {
        // Matches The "/admin/users" URL
    });
});
```

# Konklusi

Itulah kemungkinan beberapa yang sering digunakan dalam development, selengkapnya kamu bisa baca dokumentasi di website resminya. Terima kasih sudah membaca tulisan ini dan semoga bermanfaat.

# Referensi

 [## Routing

### The most basic Laravel routes accept a URI and a closure, providing a very simple and expressive method of defining…

laravel.com](https://laravel.com/docs/8.x/routing#named-routes) 

Series : Laravel

*   [[Part 1] — Salam Kenal, Saya Laravel 8](https://pandhuwibowo.medium.com/part-1-salam-kenal-saya-laravel-8-6e9d75099939)
*   [[Part 2] — Menginstall Laravel (Laravel 8)](https://pandhuwibowo.medium.com/part-2-menginstall-laravel-laravel-8-72e27fa98fcd)
*   [[Part 3] — Konfigurasi dan Mengenal Struktur Folder Laravel 8](https://pandhuwibowo.medium.com/part-3-konfigurasi-dan-mengenal-struktur-folder-laravel-8-450089601c42)
*   [[Part 4] — Mengenal Routing Laravel 8 | Part 1](https://pandhuwibowo.medium.com/part-4-mengenal-routing-laravel-8-94a41daaa90f)
*   [Part 4] — Mengenal Routing Laravel 8 | Part 2 [Sekarang disini]

[Call Friends]

Halo teman teman, untuk mendukung agar saya tetap bisa membuat tulisan-tulisan menarik lainnya. Kamu bisa support saya dengan membeli produk-produk asli produksi sendiri, homemade, dan yang pastinya brand lokal hanya di [@beneteen](https://www.instagram.com/beneteen/) atau ke [beneteen.com](https://beneteen.com/)