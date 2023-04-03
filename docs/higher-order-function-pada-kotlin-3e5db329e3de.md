# Higher-Order Function pada Kotlin

> 原文：<https://medium.easyread.co/higher-order-function-pada-kotlin-3e5db329e3de?source=collection_archive---------5----------------------->

![](img/a0947bd37e3e4cdba70085d3c3db05de.png)

Photo by Cristofer Jeschke from [https://unsplash.com/@cristofer](https://unsplash.com/@cristofer)

Secara garis dasarnya, kita mengenal apa itu fungsi. Fungsi itu adalah suatu blok kode yang kita buat untuk menjalankan suatu tugas tertentu, lalu bisa kita panggil fungsi tersebut pada tempat dimana kita membutuhkannya.

Di dalam Kotlin, ada istilah Higher-Order Function, apa itu? Secara dasarnya, Higher-Order Function itu adalah fungsi yang menggunakan fungsi lainnya sebagai paramater dan bisa juga dijadikan sebagai nilai balikan.

# Ciri-ciri Higher-Order Function

1.  Variabel dapat di isi nilainya dengan fungsi ini.
2.  Dapat dilewatkan sebagai argumen.
3.  Fungsi ini dapat dibalikan/return.

Contoh sederhananya seperti dibawah ini :

Dimana getUser() dan getAddress() adalah Higher-Order Function. Fungsi tersebut menjadikan fungsi lain sebagai salah satu parameternya, yaitu yourName dan yourAddress (yourName dan yourAddress adalah lambda function).

Kalian bisa perhatikan variable userName dan userAddress, dimana 2 variabel tersebut dapat diisi oleh fungsi yang sudah dilewatkan sebagai parameter ketiga dari fungsi getUser() dan getAddress(), yaitu yourName dan yourAddress.

Demikian penjelasan singkat dari saya mengenai Higher-Order Function. Semoga dapat dipahami hehe.