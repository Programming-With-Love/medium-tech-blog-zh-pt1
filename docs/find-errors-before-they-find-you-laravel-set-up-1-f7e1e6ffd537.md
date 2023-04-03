# Find errors, before they find you - Laravel Set-Up #1

> 原文：<https://medium.easyread.co/find-errors-before-they-find-you-laravel-set-up-1-f7e1e6ffd537?source=collection_archive---------3----------------------->

Assalamu’alaikum Warahmatullahi Wabarakatuh..

![](img/f6936c7fd1573dcb37e68bd78727b312.png)

find error

Jadi, kali ini kita akan membuat suatu set-up atau boilerplate. Bahasa sederhananya adalah “rancangan jadi” untuk aplikasi laravel kita. Yang pertama, yang akan kita siapkan adalah menjadikan aplikasi laravel kita bisa mencari atau menunjukkan error (kesalahan) sebelum meluncurkan perubahan baru pada project kita.

Proses ini sangat berguna agar kita bisa melakukan *debugging* di fase paling awal bahkan sebelum menjalankan testing untuk aplikasi kita.

> Untuk melanjutkan membaca, perlu pengetahuan dasar tentang:
> - php
> - git — version control
> - laravel
> - composer — dependency manager

## create project

Untuk membuat project baru, kita bisa meng-install menggunakan composer secara langsung, atau bisa juga menggunakan laravel-installer. Kali ini saya contohkan menggunakan laravel-installer versi `4.2.7` .

```
laravel new setup-laravel
```

![](img/4e162575a69f9d61085158ea200ab86a.png)

laravel-installer

kemudian kita bisa melakukan `initial commit` untuk meng-inisialisasi repo baru.

![](img/c17da6b0343a9effd321f0be21c06125.png)

git init

## install `phpro/grumphp`

Kita akan meng-install library `[phpro/grumphp](https://github.com/phpro/grumphp)` , library/package ini berfungsi untuk melakukan `sniffing` (meng-endus) perubahan yang terjadi, setiap kali kita akan melakukan `commit` menggunakan git.

Untuk meng-install nya kita jalankan:

```
composer require phpro/grumphp --dev
```

*kita kembali menggunakan flag `--dev` karena kita hanya menginstallnya untuk proses development saja.

![](img/ccba4e81538fb589a9395a5fe207af0e.png)

require phpro/grumphp

disini kita juga membuat sebuah file `grumphp.yml` untuk memilih task yang akan kita jalankan saat melakukan `commit` . Kita cukup menambahkan 2 task yaitu `phpcsfixer` dan `phpstan` .

Sekarang, kita memiliki sebuah file `grumphp.yml`

![](img/7c92245c7ab406b46680ee55608122cd.png)

grumphp.yml

untuk bisa melakukan `sniffing` dengan dua package diatas, kita perlu meng-install keduanya terlebih dahulu.

## install phpcsfixer

Package ini berfungsi untuk melakuan pengecekan terhadap style code dari kode php kita. Ini menjadikannya seperti bahasa static typing.

Untuk meng-install nya kita bisa jalankan:

```
composer require friendsofphp/php-cs-fixer --dev
```

![](img/a8cefeba03c55675113e5db63af35bb8.png)

require phpcsfixer

untuk detail instalasi bisa menuju link berikut [ini](https://github.com/phpro/grumphp/blob/master/doc/tasks/phpcsfixer.md) .

kemudian, sekarang kita sudah bisa menggunakan package `phpcsfixer` ini di dalam file `grumphp.yml` yang sudah dibuat di awal tadi.

Ubah isi file `grumphp.yml` menjadi seperti ini:

![](img/71f3bfc976297bbb04596fa53ee49e07.png)

grumphp-phpcsfixer

```
grumphp:
    tasks:
        phpcsfixer2:
            config: .php-cs-fixer.php
```

disitu tertulis bahwa konfigurasi dari `phpcsfixer2` adalah dari file `.php-cs-fixer.php` , lalu dimana file ini?? Tenang, kita akan membuatnya sekarang 😅

buatlah file dengan nama `.php-cs-fixer.php` yang berisi kode berikut:

sampai disini kita sudah selesai dalam meng konfigurasi `phpcsfixer` untuk bisa dijalankan menggunakan `grumphp.yml` .

Untuk mengecek apakah kita sudah bisa menjalankan `phpcsfixer` ini, kita bisa menjalankan perintah berikut:

```
vendor/bin/php-cs-fixer fix --dry-run --diff
```

![](img/8a6d8e9fba005f4336b53c9fbe4cccab.png)

phpcsfixer check

Dan ketika kita melakukan `commit` , pesan sukses dari `grumphp` sudah bisa kita lihat disini:

![](img/49401d411a5e7e0ca0363b77228e8bc3.png)

## install phpstan (with larastan)

Package phpstan, berfungsi untuk menemukan kesalahan dalam kode, tanpa harus benar-benar menjalankannya. Jadi ketika ada fungsional dari kode yang tidak sesuai, maka akan muncul peringatan saat kita melakukan `commit` .

Untuk menginstalnya, kita bisa menjalankan perintah:

```
composer require phpstan/phpstan --dev
```

![](img/bae261013720ae65ac0e532d1ead80dc.png)

require phpstan/phpstan

Kemudian, untuk menggunakan `phpstan` kita bisa menjalankan perintah:

```
vendor/bin/phpstan analyse app tests
```

![](img/c17ad90d4bead597197a59a36c4fe62f.png)

Implementasi `phpstan` ke `grumphp` :

Ubah sintaks di dalam file `grumphp.yml` menjadi seperti ini:

```
grumphp:
    tasks:
        phpcsfixer:
            config: .php-cs-fixer.php
        phpstan:
            configuration: phpstan.neon
            use_grumphp_paths: false
```

Sekarang, jika kita lakukan `commit` maka akan muncul error bahwa file `phpstan.neon doesn't exist` atau tidak ada.

![](img/762cc438ffd5c96e944683c5de07464c.png)

error phpstan.neon

Kita perlu membuat sebuah file dengan nama `phpstan.neon` yang berisi seperti dibawah ini:

```
includes:
    - ./vendor/**nunomaduro/larastan**/extension.neon

parameters:

    paths:
        - app

    # The level 8 is the highest level
    level: 5

    ignoreErrors: excludePaths:

    checkMissingIterableValueType: false
```

Kemudian kita juga perlu meng-install `nunomaduro/larastan` , untuk menyesuaikan rules dari phpstan agar sesuai dengan project laravel kita.

```
composer require nunomaduro/larastan --dev
```

![](img/3da87a249ae2433b29456220ba98d990.png)

require nunomaduro/larastan

Jika kita lakukan `commit` , maka akan tampil hasil dari pengecekan menggunakan dua package diatas.

![](img/b3b736ee9d3ec2eaaa3f11261bc388d3.png)

commit with grumphp

Sampai disini, kita sudah selesai untuk set-up project kita menggunakan `grumphp` untuk menjalankan `phpcsfixer` dan `phpstan` .

Tips untuk menjalankan manual kedua library diatas, kita bisa menambahkan script di `composer.json` agar perintahnya lebih singkat.

![](img/efea000bca48be0176885f865c3e8b39.png)

add script

```
"scripts": { ...OTHER COMMAND...

       "cs-check": [
            "vendor/bin/php-cs-fixer fix --dry-run --diff"
        ],
        "cs-fix": [
            "vendor/bin/php-cs-fixer fix --diff"
        ],
        "analyze": [
            "vendor/bin/phpstan analyze"
        ],
}
```

Ini akan mempermudah menjalankan pengecekan satu-per-satu package diatas.

![](img/7e5671ad0af5c29e3110602b96b676c3.png)

composer cs-check

![](img/25fc6b1fcc51b709660e1dafed01c556.png)

composer cs-fix

![](img/d206aa23c7d74dc1c38b60d81c795437.png)

composer analyze

Sekian. Semoga bermanfaat.

Reponya bisa dilihat di github:

[https://github.com/syofyanzuhad/laravel-setup](https://github.com/syofyanzuhad/laravel-setup)