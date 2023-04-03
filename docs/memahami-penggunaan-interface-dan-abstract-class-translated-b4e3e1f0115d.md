# Memahami Penggunaan Interface dan Abstract Class #translated

> 原文：<https://medium.easyread.co/memahami-penggunaan-interface-dan-abstract-class-translated-b4e3e1f0115d?source=collection_archive---------1----------------------->

## Kapan dan Bagaimana cara meng-implementasi-kan abstract dan interface?

Assalamu’alaikum Warahmatullahi Wabarakatuh…

![](img/8a752c3cefe7f461d38393c3cef269e6.png)

abstract and interface (from [unsplash](https://unsplash.com/photos/wQLAGv4_OYs))

# Interface Class

Seperti yang kita ketahui, sebuah *interface* di-definisikan dengan *keyword* `interface` dan semua *method* nya adalah *abstract* . Semua *method* yang di-deklarasikan di sebuah *interface* haruslah *public* ; ini adalah sifat dari *interface* .

Sebagai contoh berikut ini:

forked from: [this](https://gist.github.com/nahidulhasan/39b84eef2bf77a8893d0269cacdc3d4e#file-interface-php)

Di dalam sebuah *interface* , *method body* nya tidak di-definisikan, hanya sebuah nama dan parameter-nya. Jika kita tidak menggunakan *interface* , apa masalah yang akan muncul?
Kenapa kita perlu menggunakan *interface* ?

Anda akan mendapatkan jawaban dari pertanyaan diatas, di dalam artikel ini. Coba lihat kode di bawah ini:

forked from: [this](https://gist.github.com/nahidulhasan/bed8396c5b8c82e6da30a23a7b7b3901)

Di contoh diatas, kita tidak menggunakan *interface* . Saya akan menulis *log* menggunakan *class* `LogToFile` .

Tapi, jika saya ingin menulis log dengan `LogToDatabase` , Saya harus mengubah secara ( *hard-code)* manual *class refenrence* pada kode diatas di baris 23.

Baris kodenya seperti ini:

```
public function __construct(LogToFile $logger)
```

Kode ini akan diubah menjadi:

```
public function __construct(LogToDatabase $logger)
```

Di project yang besar, jika kita memiliki multiple class dan kita ingin mengubahnya, kita harus nengubah semuanya secara manual.

Tapi, jika kita menggunakan interface, masalah ini akan terselesaikan; kita tidak butuh untuk mengubah kodenya secara manual.

Sekarang, lihatlah kode berikut, dan cobalah untuk dipahami apa yang terjadi jika kita mengunakan `interface` :

forked from: [this](https://gist.github.com/nahidulhasan/034f5f6187b9886539eddfdf384de5a5)

Sekarang, jika kita ingin mengubah dari `LogToFile` menjadi `LogToDatabase` , kita tidak perlu mengubah *constructor method* nya secara manual. Di *constructor method* , kita telah meng- *inject* sebuah *interface* . Bukan sembarang *class* .

Jika Anda memiliki *multiple classes* dan ingin menukar nya dari satu ke yang lainnya, Anda akan mendapatkan hasil yang sama tanpa harus merubah *class* yang menjadi *reference* nya.

Pada contoh di atas, saya menulis *log* menggunakan `LogToDatabase` dan sekarang saya ingin menulis *log* menggunakan `LogToFile` .

Kita dapat memanggilnya seperti ini:

```
$controller = new UsersController(new LogToFile);
$controller->show();
```

Kita akan mendapatkan hasil, tanpa harus mengubah class yang lain karena `interface` sudah meng-handel issue (masalah) penukaran ini.

# Abstract Class

Abstract Class adalah sebuah *class* yang hanya digunakan sebagian saja implementasi-nya oleh programmer. Di dalamnya bisa saja hanya berisi setidaknya satu `abstract` *method* , yang bisa saja satu *method* itu tidak berisi kode aslinya — hanya nama dan parameter-parameter nya saja, dan itu sudah bisa dibilang sebagai “ *abstract* ”.

Sebuah *abstract* *method* sebenarnya adalah sebuah definisi *function* yang memberi tahu programmer bahwa *method* tersebut harus di implementasi-kan di *child-class* nya.

Contohnya seperti ini:

forked from: [this](https://gist.github.com/nahidulhasan/d912e33567bc13d7ab8dcef1b0acb916)

Sekarang, pertanyaannya adalah “Bagaimana kita tahu bahwa sebuah *method* itu dibutuhkan dan harus di-implementasi-kan?”. Saya akan menjelaskannya disini.

Mari kita lihat *class* `Tea` berikut:

forked from: [this](https://gist.github.com/nahidulhasan/8655cf06c770bc8af5ffbe4ea3ce7a80)

Sekarang, mari kita lihat *class* `Coffee` berikut ini:

forkerd from: [this](https://gist.github.com/nahidulhasan/93ea93762a750229a8c38cf3936e3b98)

Di dua class diatas, ada tiga method yang sama yaitu `addHotWater()` , `addSugar()` , dan `addMilk()` .

Maka, kita harus menghilangkan kode yang ter-duplikasi. Kita bisa melakukannya seperti ini:

forked from: [this](https://gist.github.com/nahidulhasan/c22a15f8bb9fb3cc90aced789f173b02)

Saya membuat `abstract` *class* dan menamainya `Template` .

Disini, Saya mendefinisikan *method* `addHotWater()` , `addSugar()` , `addMilk()` , dan membuat *abstract* *method* dengan nama `addPrimaryToppings()` .

Sekarang, jika kita membuat *class* `Tea` *extend* ke class `Template` , Kita akan mendapatka ketiga *method* yang di definisikan dan kita juga harus mendefinisikan *method* `addPrimaryToppings()` nya. *Class* `Coffee` pun akan kembali bisa digunakan dengan cara yang sama.

*Dan jika Anda ingin mempraktekannya dan mendapatkan kode tentang* `*interface*` *dan* `*abstract*` *classes, silahkan untuk melihat* [*Github*](https://github.com/nahidulhasan/oop) *ini (Star sangat dihargai) .*

Terima kasih sudah membaca.

> **Tulisan ini adalah terjemah dari tulisan asli berikut** [**ini**](https://betterprogramming.pub/understanding-use-of-interface-and-abstract-class-9a82f5f15837)