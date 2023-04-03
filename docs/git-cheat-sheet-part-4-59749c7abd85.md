# Git Cheat Sheet — Part 4

> 原文：<https://medium.easyread.co/git-cheat-sheet-part-4-59749c7abd85?source=collection_archive---------0----------------------->

![](img/fc913931d7dd4b52f6f27c70db341edd.png)

Photo by [Kelly Sikkema](https://unsplash.com/@kellysikkema?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Halo teman-teman selamat datang di blog saya. Kali ini saya akan menulis mengenai **Cheat Sheet pada Git** . Yukk.

# Git Configuration

Konfigurasikan informasi pengguna untuk semua repositori lokal.

*   Tetapkan nama yang akan dilampirkan ke komit dan tag kamu. Caranya seperti ini.

```
$ git config --global user.name "Nama Kamu"
```

*   Setel alamat email yang akan dilampirkan ke komit dan tag kamu.

```
$ git config --global user.email "kamu@example.com"
```

*   Mengizinkan beberapa pewarnaan output Git. (Opsional)

```
$ git config --global color.ui auto
```

# Setup dan Init

*   Menginisialisasi direktori yang ada sebagai repositori Git.

```
$ git init
```

*   Mengambil repositori dari lokasi yang dihosting melalui URL.

```
$ git clone [url]
```

# Day-To-Day Work

*   Menampilkan status direktori kerja kamu. Opsi termasuk baru, dipentaskan, dan file yang dimodifikasi. Ini akan mengambil nama cabang, komit saat ini pengenal, dan perubahan menunggu komit.

```
$ git status
```

*   Menambahkan suatu file ke area staging. Gunakan sebagai ganti jalur file lengkap untuk menambahkan semua file yang diubah dari direktori saat ini ke pohon direktori.

```
$ git add [file]
```

*   Hapus status file sambil tetap mempertahankan perubahan dalam direktori kerja.

```
$ git reset [file]
```

*   Menampilkan perubahan antara working directory dengan staging area.

```
$ git diff [file]
```

*   Menampilkan perubahan apa pun antara staging area dengan repository.

```
$ git diff --staged [file]
```

*   Buang perubahan dalam working directory. Operasi ini tidak dapat dipulihkan.

```
$ git checkout -- [file]
```

*   Buat komit baru dari perubahan yang ditambahkan ke area pementasan.
    Komit harus memiliki pesan!
*   Single Message / One Line Message.

```
$ git commit -m '[Commit Message]'
```

*   Multiple Message / Multiple Line Messages.

```
$ git commit
```

*   Menghapus file dari working directory dan staging area.

```
$ git rm [file]
```

*   Simpan perubahan saat ini di direktori kerja kamu untuk digunakan nanti.

```
$ git stash
```

*   Terapkan konten simpanan yang disimpan ke dalam direktori kerja, dan hapus simpanan.

```
$ git stash pop
```

*   Hapus spesifik stash dari semua stash-stash kamu sebelumnya.

```
$ git stash drop
```

[Call Friends]

Halo teman teman, untuk mendukung agar saya tetap bisa membuat tulisan-tulisan menarik lainnya. Kamu bisa support saya dengan membeli produk-produk asli produksi sendiri, homemade, dan yang pastinya brand lokal hanya di [@beneteen](https://www.instagram.com/beneteen/) atau ke [beneteen.com](https://beneteen.com/)