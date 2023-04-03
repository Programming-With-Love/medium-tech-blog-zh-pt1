# Today I Learned: MySQL Select … IN(…) secara terurut

> 原文：<https://medium.easyread.co/today-i-learned-mysql-select-in-secara-terurut-3c01de0f678c?source=collection_archive---------6----------------------->

## Bagaimana Implementasi ORDER BY IN ketika menggunakan SELECT IN query di MySQL

![](img/2eba64f6c761dfe3ae2f56c5c8dfaa4d.png)

Photo by [Edu Grande](https://unsplash.com/@edgr?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Hari ini, saya belajar satu hal baru. Mungkin ini hal yang cukup lama dan beberapa orang bisa saja sudah mengetahuinya, bahkan mungkin cukup lazim, tetapi buat saya sendiri ini adalah hal yang baru saya ketahui.

Penemuan ini berawal ketika kami sedang mengerjakan fitur di kantor saya, Kurio. Dimana ketika mengerjakan fitur ini, kami diharapkan untuk melakukan *query* ke database dengan spesifik ID yang akan dimasukkan oleh *client* *service* kami, seperti

```
GET /articles?ids=122,123,151,111,231,102
```

Berhubung sistem kami menggunakan RDBMS sebagai *data-store* , spesifiknya, MySQL. Maka untuk melakukan ini bisa dibilang cukup mudah.

Yang pastinya flownya adalah sebagai berikut.

*   Parse request query-param dari *client,* lalu
*   Lakukan Query ke MySQL Database.

Berdasarkan *requirement* yang diharapkan, ketika *client* melakukan request seperti ini

```
GET /articles?ids=122,123,151,111,231,102
```

Maka *result* *response* yang diharapkan adalah *articles* yang diberikan harus berurut sesuai urutan yang diminta. Jika dibuat dalam *response* JSON kira-kira seperti ini.

```
[
  {
    "id": "123",
    "title": "Asus ZenWatch review: Android Wear made elegant"
  },
  {
    "id": "151",
    "title": "Pemkab Dinilai Belum Mampu Tuntaskan Akar Konflik"
  },
  {
    "id": "111",
    "title": "Alcatel Announces Budget-Minded Smartwatch"
  },
  {
    "id": "231",
    "title": "Identitas Geng Motor di Tulehu Misterius"
  },
  {
    "id": "102",
    "title": "[APK Download] YouTube Update Adds A New Share Menu And Brings Back Voice Search"
  }
]
```

Biasanya untuk urusan seperti ini, yang saya lakukan adalah *query* ke DB, dan *sorting* lagi di dalam *function* aplikasi saya. Namun berawal dari rasa penasaran, bisa *gak* sih dari database langsung terurut tanpa harus sorting lagi?

## The Old Ways

Seperti yang saya sebutkan diatas, biasanya saya melakukan *query* dari database, lalu saya urut di fungsi aplikasi saya. Untuk querynya adalah dengan menggunakan dengan kondisi `IN` . Berikut cara saya query.

```
SELECT id, title FROM article WHERE id IN(123,151,111,231,102)
```

Namun dengan query ini, MySQL akan memberikan *rows* yang tidak berurut sesuai urutan ID yang saya inginkan. Melainkan data yang diberikan berurut secara default ascending by ID. Contohnya adalah sebagai berikut.

Sehingga alih-alih pengen cepat tanpa sorting di aplikasi, yang saya harapkan adalah, data yang diberikan dari database harus sudah berurut sesuai urutan ID yang saya inginkan. Yang saya inginkan kira-kira seperti ini.

**Perhatikan urutan rowsnya, dia berurut berdasarkan urutan ID yang saya berikan, bukan ascending ataupun descending, melainkan berdasarkan urutan yang saya berikan di query* `*IN*` *.*

## MySQL Order By Field Selector

Solusinya adalah, saya pun berselancar didunia maya, cukup singkat, karena ini sudah rahasia umum, yang dimana saya baru mengetahuinya. Sehingga sourcenya cukup banyak jika dicari di Google.

Caranya adalah dengan menggunakan `FIELD` selector pada query kita. Sehingga query saya yang sebelumnya seperti ini:

```
SELECT id, title FROM article WHERE id IN(123,151,111,231,102)
```

Saya ubah menjadi seperti ini.

```
SELECT id, title, content FROM article WHERE id IN(123,151,111,231,102) **ORDER BY FIELD(id, 123,151,111,231,102)**
```

Dan hasilnya akan men-select rows berdasarkan urutan id yang kita berikan, bukan ascending ataupun descending melainkan terurut langsung berdasarkan id yang diberikan.

Kira-kira cuma itu saja terkait hal ini.

## Referensi

*   [https://stackoverflow.com/a/958642](https://stackoverflow.com/a/958642)
*   Thanks to [Vegeterrixen Gousander](https://medium.com/u/93565e847392?source=post_page-----3c01de0f678c--------------------------------)