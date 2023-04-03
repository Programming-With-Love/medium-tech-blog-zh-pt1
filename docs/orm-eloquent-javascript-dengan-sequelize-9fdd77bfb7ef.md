# Rindu dengan Eloquent Laravel? Di Javascript Juga Ada!

> 原文：<https://medium.easyread.co/orm-eloquent-javascript-dengan-sequelize-9fdd77bfb7ef?source=collection_archive---------1----------------------->

![](img/f925d89791969e43494945409d09079d.png)

Photo by [Denin Williams](https://unsplash.com/photos/hVF_04fzKO4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/simple-and-pretty?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

Hai! Kamu pengguna laravel? Gimana perasaan kamu saat pindah dari native php terus pakai framework kayak laravel?

Bahagia.

Terutama buat programmer males kayak saya. Laravel itu cocok banget.

Laravel itu udah kayak semua fitur all in ada di dalam satu framework siap pakai.

Kalau kamu apa fitur favorit yang ada di laravel?

Ada banyak.

Haha ya memang banyak banget fitur keren yang ada di laravel. Salah satu nya fitur Eloquent atau ORM bahasa umumnya.

[](https://laravel.com/docs/5.6/eloquent#introduction) [## Eloquent: Getting Started - Laravel - The PHP Framework For Web Artisans

### Laravel - The PHP framework for web artisans.

laravel.com](https://laravel.com/docs/5.6/eloquent#introduction) 

Kenapa saya suka dengan Eloquent?

Karena didalamnya ada fitur migration, model dan penggunaan operasi databasenya yang sangat mudah.

Dengan migration, kita bisa mengatur pembuatan table dengan file. karena berbasis file. Artinya bisa ke track dengan git.

Dengan begitu ketika kerja bareng, akan sangat mudah untuk menshare hasil database yang kita buat dengan rekan yang lain. Jadi gak ada lagi, copy-copy manual database terus di share pakai flashdik, mengenang jaman dulu 😆

Dan pastinya di setiap komputer rekan kita punya database yang persis sama.

cukup tentang migration, lalu tentang model dan operasi databasenya.

Biasanya kita kalau mau melakukan operasi database, pertama yang harus dipikirkan adalah bagaimana membuat query nya.

Lalu, kita harus menuliskan string query nya secara manual.

Dan jalankan query nya, selesai.

Hasilnya, kode kita akan penuh dengan query-query database. pas lihat kode jadi pusing, karena banyak banget tulisan querynya.

Nah, disinilah eloquent berperan. Daripada kita ngetik query secara manual, eloquent akan ngebantu kita dengan hanya menggunakan method nya yang super cantik dan singkat.

Eloquent Example Code

Dengan begitu kode kita jadi mudah di baca dan rapih.

“Mantap mas, saya setuju sekali, tapi kayak nya judul artikel ini bukan tentang laravel ya, tapi tentang javascript?”.

Nah itu.

Sangking seneng cerita suka-suka nya laravel (enggak ada dukanya), jadi lupa.

Gimana kalau kita dapet project dan harus di buat dengan back end javascript? (nodejs)

Aduuh, sedih. gak kuat ninggalin kerennya laravel.

Tenang dulu.

Ternyata di javascript ada di [**Sequelize**](http://docs.sequelizejs.com/) **.**

Eloquent atau ORM nya versi javascript.

Dengan sequelize ini kita bisa gunakan berbagai database basis sql seperti mysql, postgre, sqlite, mssql

Bedanya ini promise based dan ini javascript. **Apa itu promise** ? Nantikan artikel selanjutnya, saya akan bahas promise. Karena pembahasa promise lumayan panjang dan njelimet.

Orang bilang. “Show me the Code”

Ini contoh penggunaan code nya untuk query select.

Lagi ngulik project javascript dan berurusan dengan database, pasti rindu dengan eloquent laravel.

Langsung aja meluncur ke [dokumentasi nya sequelize](http://docs.sequelizejs.com/) . Sudah lengkap dan jelas.

[](http://docs.sequelizejs.com/) [## Manual | Sequelize | The node.js ORM for PostgreSQL, MySQL, SQLite and MSSQL

### Sequelize will setup a connection pool on initialization so you should ideally only ever create one instance per…

docs.sequelizejs.com](http://docs.sequelizejs.com/) 

*Jika anda merasa artikel ini menarik dan bermanfaat, silahkan* ***berikan claps*** *👏 👏 sebanyak-sebanyaknya dan* ***bagikan*** *ke lingkaran pertemanan anda, agar mereka dapat membaca artikel ini. Dan jangan lupa* [***follow saya di medium***](https://medium.com/@haidarafifmaulana) *untuk terus dapatkan tulisan seperti ini setiap minggunya.*