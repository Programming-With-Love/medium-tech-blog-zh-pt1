# Menambahkan Proyek Android Studio Ke Github Menggunakan Terminal

> 原文：<https://medium.easyread.co/menambahkan-proyek-android-studio-ke-github-menggunakan-terminal-f95f33864e69?source=collection_archive---------0----------------------->

![](img/b567ed61194a7cc56fbad5f30e98315f.png)

Saya ingin mencoba untuk mendokumentasikan apa yang telah saya kuasai di platform ini dan ingin sharing dengan sesama, semoga akun ini tidak kena suspend lagi hehe.

Pada tulisan ini, saya ingin mencoba sharing tentang “Bagaimana cara menambahkan proyek aplikasi Android Studio ke Github (VCS) menggunakan Terminal”. Didalam Android Studionya sendiri sudah ada fitur push ke dalam Github yaitu “Share project on Github”, anda dapat dengan mudah menambahkan proyek aplikasi anda ke dalam Github dengan beberapa klik saja. Namun, untuk menambah pengetahuan saja dan bisa anda terapkan di perusahaan tempat anda bekerja sebagai Programmer/Developer atau posisi yang berkaitan dengan dunia Software Development, tentunya pengetahuan tentang git ini memang wajib untuk dikuasai, minimal anda bisa :

1.  Buat repository di Github.
2.  Inisialisasi git didalam proyek aplikasi kita.
3.  Menambahkan remote pada aplikasi yang sedang kita kembangkan/develop agar dapat terkontrol.
4.  Commit & Push proyek aplikasi ke dalam Github.

Pada tulisan ini, kita akan mencoba beberapa hal tersebut diatas.

Pertama yang harus kita lakukan adalah membuat repository di Github. Anda bisa mengunjungi website [Github](https://github.com/) , lalu anda bisa mencoba membuat repository baru dengan klik “New repository” pada tombol berwarna hijau.

![](img/68dc550b557b641fa41a1de8d1c162c4.png)

Anda akan diarahkan ke halaman ini untuk membuat repository dengan nama dan deskripsi yang anda inginkan.

![](img/b3080325a64f2b24cdf5c98acda5318a.png)

Tekan tombol “Create repository”, setelah itu anda akan diarahkan ke halaman ini.

![](img/126072cd6929ef5458a8a4375f575ffa.png)

Langkah selanjutnya adalah anda bisa kembali ke proyek Android Studionya, buka tab Terminal di bagian bawah Android Studio, lalu ketik command ini :

Untuk inisialisasi git pada proyek Android Studio anda.

```
git init
```

![](img/9f93f38c777e44be8f3c3b7747632402.png)

Menambahkan remote pada aplikasi agar dapat terkontrol dengan Github.

```
git remote add origin [https://github.com/mahesaiqbal/TestingRepo.git](https://github.com/mahesaiqbal/TestingRepo.git)
```

![](img/af7977491b87812a9344e7d277011aec.png)

Menambahkan semua file/memperbarui semua file yang ada.

```
git add .
```

![](img/95c2be7a40e763cb0457790f37239e23.png)

Commit & Push aplikasi anda ke Github

```
git commit -m "Ini adalah pesan commit pertama anda"
```

![](img/5540a8fe6a662ef09bf4bfb501161662.png)

```
git push -u origin master
```

![](img/9e73a64443e6f5760f39e233da84c3a2.png)

Sekarang, anda bisa reload halaman web yang sebelumnya dan akan tampil seperti ini.

![](img/010890e594f958791201d80ef2ff232a.png)

Yeayy! sekarang proyek aplikasi Android Studio anda sudah berhasil ditambahkan ke Github anda.

Selamat mencoba ^_^ happy coding!