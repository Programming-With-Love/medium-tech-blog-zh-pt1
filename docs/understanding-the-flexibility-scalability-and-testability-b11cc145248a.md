# Understanding the Flexibility, Scalability and Testability

> 原文：<https://medium.easyread.co/understanding-the-flexibility-scalability-and-testability-b11cc145248a?source=collection_archive---------8----------------------->

![](img/c6d366c2c8c7adb28954ce02197a6e78.png)

Photo by [Eric Muhr](https://unsplash.com/@ericmuhr?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

> *Ikatlah Ilmu dengan Menuliskannya. — Sayyidina Ali bin Abi Thalib r.a.*

# **Pendahuluan**

*Interface* merupakan salah satu hal yang paling sulit dimengerti oleh kebanyakan orang (termasuk saya), ketika memulai belajar bahasa pemrograman Go. Saya dulu sering sekali menanyakan apa fungsi dari *interface* dan kenapa kode yang saya tulis harus menggunakan *interface* lagi, padahal tanpa *interface* pun kode saya berjalan tanpa error 🤔

Sering kali saya melihat orang-orang mengatakan *interface* ini digunakan untuk ini atau untuk itu, tetapi saya tetap menolak pemahaman tersebut sambil berpikir “ *loh kalau fungsinya hanya itu saja, tanpa interface bisa-bisa saja tuh* ”

Entah karena orang-orang tersebut yang kurang memberi penjelasan yang dapat dicerna oleh saya atau ilmu saya yang belum mencapai tingkat tersebut untuk memahami teori yang dijelaskan karena belum pernah menemui kasus dimana harus menggunakan *interface* dan ujung-ujungnya saya tetap menolak untuk menerima penjelasan tersebut.

Dalam perjalanan saya, saya tetap tidak bisa menerima keuntungan dari penggunaan *interface* apabila orang lain menjelaskannya kepada saya. Sampai suatu ketika saya menemukan kasus dimana harus menggunakan *interface* dan *voilaaa* , disanalah saya mengerti kenapa *interface* itu sangat penting dan fungsinya sangat keren.

Bagi saya, kalau ditanya apa fungsi *interface* , saya akan menjawabnya dengan 3 hal, yaitu ***Flexible, Scalable and Testable*** .

*Interface* akan membuat kode kita menjadi *flexible* , *scalable* dan *testable* . Saya jamin orang yang belum mengerti fungsi dari *interface* akan kebingungan mencari maksud dari jawaban saya dan mudah-mudahan orang yang sudah mengerti *interface* juga sepakat dengan saya 😄.

Melalui tulisan ini, saya akan mencoba membuat studi kasus untuk membantu kita mengerti apa itu sebenarnya fungsi dari *interface* . Harapannya setelah membaca tulisan ini kita akan mengerti apa itu *interface* dan bisa mendefinisikan fungsi *interface* berdasarkan pemahaman masing-masing.

# **Studi Kasus**

Marp bekerja sebagai seorang *Software Engineer* di suatu perusahaan di Jakarta. Di perusahaannya, Marp menggunakan bahasa pemrograman Go untuk membuat suatu *software.* Pada suatu sore, tiba-tiba sang bos memanggil dan berbicara:

> Marp, tolong dong bikinin aku satu function geometri dua dimensi dengan nama functionnya luas, function itu bisa menerima informasi tentang bangun dua dimensi apapun bentuk bangun-nya sebagai parameter dan menghitung luas dari bangun tersebut.

Marp lantas kembali ke meja, membuka editor favoritnya yaitu Visual Studio Code, membuka Spotify, memutar *playlist* -nya dan mulai memasuki alam pikirannya dan melupakan dunia nyata.

Marp berhasil membuat kode pertama dan berjalan dengan mulus. Pada kode pertama ini Marp membuat 1 *function* dengan nama `**luas**` dan menerima 2 parameter `**width**` dan `**height**` dengan tipe `**float64**` dan juga dengan `**float64**` sebagai *return type* -nya. Fungsi ini dengan sederhana hanya menghitung luas persegi panjang dengan rumus (panjang x lebar).

Kode ini berhasil menghitung luas, tetapi hanya sekedar luas persegi panjang. Tahap selanjutnya Marp berpikir bagaimana untuk membuat fungsi luas ini bisa menghitung bangun lain.

## **Struct**

Marp seketika berpikir untuk menambahkan *struct* pada kode nya dan mengenkapsulasi kodenya menjadi

Marp mengubah kode sebelumnya dan sekarang kodenya pun terenkapsulasi setelah menambahkan type *struct* dengan nama PersegiPanjang pada kodenya.

Selanjutnya Marp berpikir untuk mencoba menambahkan *type* *struct* Segitiga pada kodenya dan membuat fungsi untuk menghitung luas segitiga

Marp membuat fungsi baru dengan nama luas seperti fungsi luas sebelumnya, kali ini marp membuat fungsi luas dengan parameter tipe Segitiga, Marp berasumsi mungkin bisa dibuat dua fungsi dengan nama yang sama asalkan menerima parameter dengan tipe yang berbeda

Sayangnya tidak. Ketika kode ini dijalankan Marp mendapatkan error

```
# command-line-arguments
./main.go:19:6: luas redeclared in this block
 previous declaration at ./main.go:10:42
```

Marp akhirnya sadar kalau cara seperti ini tidak bisa digunakan, akhirnya ia pun memikirkan cara lain untuk mengatasi masalah ini.

## **Method**

Marp terpikir untuk menggunakan *method* dan *struct* - *struct* nya sebagai *receiver* .

Kali ini Marp berhasil membuat kode ini berjalan, cukup dengan memanggil method persegiPanjang.Luas() atau segitiga.Luas() maka kode ini akan menghitung luas dari bangun yang bersangkutan.

Marp berbicara dengan bosnya dan menunjukan kode yang ia sudah buat dan mendapat respon

> Oke kode kamu sudah bagus berjalan dan hampir sempurna, tapi saya mau lebih dari ini, saya mau hanya ada 1 fungsi general bernama luas, fungsi tersebut menerima parameter informasi-informasi dari bangun yang ingin dihitung luasnya, that’s it. Jadi aku maunya dibalik, bukan bangun.Luas() tapi aku maunya luas(bangun).

Marp dalam hati berbicara “ *Ada-ada aja permintaan si bos ini, padahal kan kaya gini juga sudah jalan* ”.

Marp kembali ke mejanya dan memikirkan bagaimana membuat satu fungsi general untuk menghitung luas. Seandainya Marp menggunakan bahasa pemrograman *dynamic-type* hal ini akan sangat mudah dilakukan, tinggal buat saja fungsi-nya lalu lempar apapun itu tipe bangunnya sebagai parameter. Sayangnya Marp tidak bisa melakukan hal itu mengingat Marp menggunakan bahasa pemrograman Go yang mana merupakan *static-type* .

## **Interface**

Marp sudah sangat pusing dan tidak tahu harus bagaimana lagi, lalu ia membuka dokumentasi Go dan tiba-tiba ia terpikir untuk mencoba menggunakan interface dan *VOILAA* !

Marp membuat satu *interface*

```
type GeometryCalculator interface {
    Luas() float64
}
```

dan membuat satu *function*

```
func luas(gc GeometryCalculator) string {
    *return* fmt.Sprintf("Luas: %0.1f", gc.Luas())
}
```

Begilah kode lengkap versi terbarunya

Dalam tahap ini, Marp sudah berhasil membuat satu *function* bernama luas yang menerima informasi bangun sebagai parameternya. Dalam kondisi ini Marp berhasil membuktikan 2 manfaat dari interface yaitu membuat kode menjadi *flexible* dan *scalable* .

Dalam fungsi luas Marp hanya perlu memanggil interface.Method() dalam kasus ini gc.Luas() dan secara ajaib method yang memiliki receiver struct yang bersangkutan lah yang terpanggil.

Kode Marp menjadi *flexible* dimana function luas yang dibuat bisa menerima apapun bentuk bangun sebagai parameternya. Masih ingat ketika Marp mencoba membuat 2 fungsi luas?

```
func luas(persegi PersegiPanjang) float64 { /*/ }
func luas(segitiga Segitiga) float64 { /*/ }
```

Hasilnya? gagal…

Dengan menggunakan *interface* Marp mampu mencapai titik dimana memanggil 1 *function* dengan nama yang sama dengan parameter yang berbeda.

*Scalable* ? Tentu saja. Kode yang dibuat Marp menjadi *scalable* , apabila kedepannya ingin menambahkan bangun lain seperti misalnya lingkaran, yang perlu dilakukan hanyalah membuat *type* *struct* lingkaran, mendefinisikan *field* - *field* yang dimiliki lingkaran pada *structnya* dan *method* untuk menghitung luas lingkaran tanpa perlu merubah fungsi luas utamanya.

Coba bayangkan *function* luas tersebut menjadi suatu fungsi manipulasi *database* , dalam kasus itu kita bisa menggunakan dan mengganti berbagai database pada kode kita tanpa merubah fungsi manipulasi database tersebut. Kode kita menjadi *decoupled* .

Marp berfikir ada hal lain yang bisa ia lakukan lagi dengan mengimplementasikan *interface* ini. Lalu Marp berfikir, dengan fleksibilitas yang dimiliki oleh fungsi luas, maka ia bisa melakukan testing dengan membuat mockup struct bangunnya beserta methodnya.

Lalu Marp membuat kode testing, disini Marp membuat *mockup* untuk persegi panjang dan segitiga, Marp membuat struct FakePersegiPanjang dengan method Luas().

Marp memanggil fungsi luas yang sudah dibuat sebelumnya dan lalu melempar mockup persegi panjang ini sebagai parameternya. Berhubung struct FakePersegiPanjang memiliki method Luas() maka *struct* tersebut secara implisit mengimplementasikan *interface* GeometryCalculator maka fungsi luas pun dapat menerima FakePersegiPanjang sebagai parameternya. Hal yang sama pun dilakukan untuk kasus bangun segitiga.

```
=== RUN   TestLuas
=== RUN   TestLuas/persegiPanjang
=== RUN   TestLuas/segitiga
--- PASS: TestLuas (0.00s)
    --- PASS: TestLuas/persegiPanjang (0.00s)
    --- PASS: TestLuas/segitiga (0.00s)
PASS
```

Test berhasil dijalankan dan tidak mendapatkaan error. Sampai dititik ini Marp sudah berhasil membuat kode yang *flexible* , *scalable* dan *testable* .

Tetapi Marp belum puas, iya melihat ke kode test yang ia buat dan merasa banyak duplikasi, ia merasa kode test yang ia buat masih bisa diperbaiki.

## ***Table Driven Test***

Marp membaca satu artikel tentang testing dan mendapatkan ilmu baru, yaitu *table driven tests* , hal ini sangat berguna ketika kita ingin membuat *list* dari *test cases* yang ingin bisa kita test dengan cara yang sama

Dengan menggunakan cara ini kode test menjadi lebih bersih dan tidak memiliki duplikasi. Marp mendeklarasi dan menginisialisasi table *test* yang diberi nama luasTests dengan tipe *slice* yang berisi *struct* dengan 3 field yaitu *name, geometry* dan *want. name* akan diisi nama test yang diinginkan, *geometry* akan diisi oleh struct mockup yang digunakan pada testing sedangkan *want* akan diisi oleh *expected result* atau hasil yang diinginkan dari fungsi luas.

Dititik ini, Marp sangat puas dengan kode yang dibuatnya.

Mungkin masih ada yang bertanya-tanya, kenapa kita harus membuat *mockup* untuk melakukan test? kenapa tidak panggil saja langsung *function* /methodnya?

Hal ini benar dan bisa digunakan untuk program sederhana seperti ini, tetapi pada kasus seperti kita ingin melakukan test pada fungsi yang memanggil *http request* ke suatu API kita harus menggunakan *mockup* untuk melakukan test pada kasus tersebut. Karena kita tidak akan tahu apa respon yang akan kita dapat dari API tersebut, bisa saja ketika kita melakukan testing API tersebut sedang down dan memberikan kita respon yang berbeda. Dalam kasus tersebut sebenarnya kode kita sukses, masalah hanya ada pada API yang kita panggil, maka seharusnya test kita pun sukses, hanya saja karna API yang kita gunakan sedang *down* dan memberikan respon lain, test kita menjadi gagal :(.

Btw, cerita di atas hanyalah cerita fiktif belaka untuk menjelaskan dengan lebih baik apa itu *interface* 😃

So, does Go have *generics* ?

*Artikel ini di tulis oleh* [*Pramesti Hatta K.*](https://medium.com/u/ef69225df6f0?source=post_page-----b11cc145248a--------------------------------) *Beliau sering menulis artikel mengenai Software Engineering. Follow profilnya untuk mendapatkan update-an terbaru artikel-artikel beliau.*

*Jika anda merasa artikel ini menarik dan bermanfaat, bagikan ke lingkaran pertemanan anda, agar mereka dapat membaca artikel ini.
Atau jika anda tertarik untuk membagikan cerita anda pada publikasi ini, anda boleh mengirimkan cerita anda ataupun mengikuti langkah-langkah yang ada* [***disini***](https://medium.com/easyread/about-easyread-74b20960e180) *.*