# Pakai Sequelize, Rindu dengan migrate:refresh? ini saya buatin!

> 原文：<https://medium.easyread.co/pakai-sequelize-rindu-dengan-migrate-refresh-ini-saya-buatin-f88135044887?source=collection_archive---------1----------------------->

![](img/014111afef32f2b2b3c15d14928b6bbb.png)

Photo by [rawpixel.com](https://unsplash.com/photos/FB_3777IC10?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/refresh?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

Salah satu fitur orm laravel yang saya pakai yaitu `**php artisan migrate:refresh**` atau `**php artisan migrate:fresh**` . Fungsi nya adalah untuk membuat *database* kita fresh seperti baru di *migrate* .

Kenapa saya memakai *command* ini?
Karena sering kali terjadi *migration* yang di buat oleh orang lain dan saya tidak ingin ambil pusing jika ada masalah dengan *database* lokalsaya. Maka caranya adalah dengan mendrop seluruh *table* dan lakukan ulang *migration* .

Akan tetapi tentunya kalau kita melakukannya secara manual akan membuang-buang waktu. Maka laravel menyedikan *command* `**migrate:fresh**` untuk melakukan dua tugas tersebut, yaitu drop all table dan jalankan proses *migrate* .

Bagi kita yang sedang mengerjakan projek *node js* dan menggunakan *sequelize* sebagai orm-nya, sayangnya fitur tersebut tidak ada. Walau sebenarnya kita bisa dilakukan secara manual yaitu dengan *command* :

```
**$ sequelize db:migrate:undo:all
$ sequelize db:migrate**
```

tapi tentunya akan ada banyak *command* yang harus di ketik dan tentu saja capek. Karena saya *programmer* yang males 😈, maka saya membuat *package* `[**fresh_sequlieze**](https://www.npmjs.com/package/fresh_sequelize)` untuk menjalankan kedua *command* di atas.

[](https://www.npmjs.com/package/fresh_sequelize) [## fresh_sequelize

### Package to refresh database migrations and seeders in sequelize, inspiration from laravel migrate:fresh

www.npmjs.com](https://www.npmjs.com/package/fresh_sequelize) 

Untuk menggunakan *package* ini diharuskan menggunakan *sequelize* , karena sebenarnya fungsi *package* ini hanya menjalankan dua *command* di atas secara otomatis. Jadi kita cukup menjalankan *command* berikut, maka secara otomatis akan menjalankan `**db:migrate:undo:all**` dan `**db:migrate**`

```
**$ fresh migrate**
```

Lebih singkatkan? Dan kalau mau sekalian dengan *seeder-* nya, bisa dengan *command* berikut :

```
**$ fresh migrate seed**
```

atau hanya mau *seeder-* nya aja, bisa dengan :

```
**$ fresh seed**
```

Untuk menginstallnya :

```
**$ npm install --global fresh_sequelize**
```

*Nah* , tertarik mencoba membuat package kalian sendiri? Simak [artikel ini!](https://medium.com/easyread/cara-buat-package-node-js-sendiri-6c8b91f5c2cf)

*Jika anda merasa artikel ini menarik dan bermanfaat, silahkan* ***berikan claps*** *👏 👏 sebanyak-sebanyaknya dan* ***bagikan*** *ke lingkaran pertemanan anda, agar mereka dapat membaca artikel ini. Dan jangan lupa* [***follow saya di medium***](https://medium.com/@haidarafifmaulana) *untuk terus dapatkan tulisan seperti ini setiap minggunya.*