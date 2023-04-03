# Teks “Localization” di Golang

> 原文：<https://medium.easyread.co/teks-localization-di-golang-397a0f396b05?source=collection_archive---------0----------------------->

## Contoh Sederhana Membuat Text Localization di Aplikasi Golang

![](img/1ce95daa900723dd55c06e9e1c36bdc1.png)

Photo by [pixabay.com](https://pixabay.com/photos/bolivia-flags-salt-lake-lake-wind-2494518/)

Halo semuanya! Ini tulisan pertama saya di Medium dalam Bahasa Indonesia! Walau saya pernah menulis tentang topik ini dalam Bahasa Inggris di [https://medium.com/@bismobaruno/9787ca85b4ec](https://medium.com/@bismobaruno/text-localization-in-go-9787ca85b4ec) , semoga dengan adanya versi Bahasa Indonesia ini bisa membantu teman-teman developer Indonesia untuk bisa lebih memahami tentang materi yang akan saya bagikan disini :)

Jadi hari ini, saya ingin membagikan pengalaman saya saat menambahkan fitur untuk aplikasi *mobile* kami. Salah satu fitur yang kami kembangkan adalah tentang bahasa. Kami mempunyai sedikit masalah untuk bahasa yang digunakan di aplikasi kami. Pada dasarnya, banyak tulisan yang menggunakan Bahasa Inggris (walau beberapa dicampur menggunakan Bahasa Indonesia) seperti bagian menu, judul, informasi dan lain sebagainya. Dan karena layanan kami adalah SaaS, maka tak jarang tiap klien mempunyai permintaan bahasa yang berbeda. Maka dari itu, kami berpikir untuk membuat hal ini lebih dinamis.

Dalam tulisan ini, saya tidak akan membahas dari sisi aplikasi *mobile* , alasannya adalah karena saya seorang *Backend Developer* :) Tapi kami juga sadar, hal ini tidak bisa selesai hanya dengan melakukan perubahan dari sisi aplikasi *mobile* . Karena kami banyak menggunakan teks yang berasal dari pemanggilan API untuk ditampilkan di halaman aplikasi.

Kembali ke permasalahan tadi, sebenarnya hal yang paling mudah yang bisa dilakukan adalah dengan menggunakan *conditional statements* untuk membuat pesan itu menjadi dinamis di sisi *backend* . (Sekedar informasi, *backend* kami ditulis menggunakan bahasa Go). Contohnya:

```
language := "en" // asumsikan nilai ini kita dapat dari *request*
message := ""if language == "en" {
    message = "Good morning"
} else if language == "id" {
    message = "Selamat pagi"
} else if language == "jp" {
    message = "おはようございます"
}
```

Tapi sepertinya itu bukan solusi yang bagus, mengingat butuh banyak *effort* , susah di pelihara dan sulit dikembangkan jika kita ingin menambah beberapa bahasa lain.

> *Lalu,* goal *kami* *adalah membuat* library *untuk bisa menyelesaikan masalah tersebut.*

[](https://github.com/moemoe89/go-localization) [## moemoe89/go-localization

### go-localization is a Go package that provides tool for showing message based on language code. It has the following…

github.com](https://github.com/moemoe89/go-localization) 

Akhirnya, jadilah *library* *open source software* di Go ( [https://github.com/moemoe89/go-localization](https://github.com/moemoe89/go-localization) ) untuk mengubah suatu teks ke arti yang berbeda-beda.

Dengan pertimbangan daripada membuat fungsi yang hanya bisa dipakai secara *private* , kami lebih ingin membuat solusi ini bisa umum digunakan secara luas oleh semua orang yang ini menyelesaikan masalah yang sama.

Untuk menggunakan *library* ini dengan bahasa Go cukup mudah. Pertama, *install* *package* dengan perintah:

```
go get github.com/moemoe89/go-localization
```

Kemudian buat *file* JSON yang berisi kode dan teksnya, kemudian simpan di dalam direktori *project* kita. Mungkin bisa kita beri nama *language.json.* Contohnya:

Lalu, *import package library* tersebut, panggil *config-* nya dan mari langsung kita praktekkan!

Dengan *library* ini, kita mendapat beberapa keuntungan:

*   Pertama, kita dapat melakukan transformasi teks dengan mudah menggunakan *file* JSON.
*   Selanjutnya, kita bisa dengan mudah mengganti *key* dari suatu teks misalnya dengan kode. Sehingga akan lebih simpel.
*   Terakhir, fungsi ini tidak akan menyebabkan *break* pada kode kita. Jika kita memasukkan teks yang tidak ada pada *file* JSON, maka *output* yang didapat akan sama seperti *input.*

Akhir kata, semoga teman-teman menikmatinya! Jika ada ide atau ingin berkontribusi, silakan lakukan *Pull Request* pada *repository* tersebut!

Terimakasih!