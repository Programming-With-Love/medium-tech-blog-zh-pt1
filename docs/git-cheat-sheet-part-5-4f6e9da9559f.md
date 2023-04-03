# Git Cheat Sheet — Part 5

> 原文：<https://medium.easyread.co/git-cheat-sheet-part-5-4f6e9da9559f?source=collection_archive---------1----------------------->

![](img/af3d91e4d89a88c089fb630233d05395.png)

Photo by [DESIGNECOLOGIST](https://unsplash.com/@designecologist?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Halo teman-teman selamat datang di blog saya. Kali ini saya akan menulis mengenai **Cheat Sheet pada Git** . Yukk.

# Branches

![](img/c2288648847678376e414ace9b9903d1.png)

Photo by [Erica Nilsson](https://unsplash.com/@enphotos?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

*   Daftar branch-branch kamu.

```
$ git branch
```

*   Membuat branch baru.

```
$ git branch [branch-name]
```

*   Menghapus branch lokal

```
$ git branch -d [branch-name]
```

*   Daftar branch-branch kamu baik dilokal maupun remote branch

```
$ git branch -a
```

*   Menghapus branch remote

```
$ git push origin --delete [branch-name]
```

*   Pindah ke branch lain dan periksa ke direktori kerja kamu

```
$ git checkout [branch-name]
```

*   Pindah direktori kerja ke cabang yang ditentukan. Dengan -b: Git will
    buat cabang yang ditentukan jika tidak ada.

```
$ git checkout -b [branch-name]
```

*   Bergabunglah dengan branch yang ditentukan [dari nama] ke cabang Anda saat ini (yang satu Anda saat ini).

```
$ git merge [from branch-name]
```

# Mengembalikan Perubahan

![](img/c79c127481be77815588e11f1cc3f094.png)

Photo by [Jim Wilson](https://unsplash.com/@wilsonjim?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

*   Buat komit baru, kembalikan perubahan dari komit yang ditentukan.
    Ini menghasilkan inversi perubahan.

```
$ git revert [commit sha]
```

*   Mengurungkan semua komit setelah [komit], mempertahankan perubahan secara lokal.

```
$ git reset [commit sha]
```

*   Buang semua riwayat dan perubahan kembali ke komit yang ditentukan.

```
$ git reset --hard [commit sha]
```

# Sinkronkan Perubahan

*   Unduh semua riwayat dari branch remote.

```
$ git fetch
```

*   Menggabungkan branch remote menjadi cabang lokal saat ini.

```
$ git merge
```

*   Hapus referensi jarak jauh yang telah dihapus dari repositori jarak jauh.

```
$ git fetch --prune [remote]
```

*   Tambahkan URL git sebagai alias

```
$ git remote add [alias] [url]
```

*   Dorong branch lokal ke repositori remote. Setel salinannya sebagai upstream.

```
$ git push -u [remote] [branch]
```

*   Dorong perubahan lokal ke remote. Gunakan (- - tags) untuk mendorong tag.

```
$ git push [--tags] [remote]
```

*   Ambil perubahan dari remote dan gabungkan branch saat ini dengan
    ke hulu.

```
$ git pull [remote] [branch]
```

# Temporary Commit

![](img/f0c059ebff335c4d750e8fa474b6fb0b.png)

Photo by [Emanuel Ekström](https://unsplash.com/@emanuelekstrom?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

*   Simpan perubahan yang diubah dan bertahap.

```
$ git stash
```

*   Daftar urutan tumpukan perubahan file yang disimpan.

```
$ git stash list
```

*   Bekerja dari atas tumpukan simpanan

```
$ git stash pop
```

*   Buang perubahan dari atas tumpukan simpanan

```
$ git stash drop
```

# Konklusi

Sangat banyak yang harus kamu pahami dan praktekkan, tapi berita baiknya kamu akan lancar jika kamu sering terbiasa dengan command-command diatas dan tulisan sebelumnya. Selamat mencoba dan semoga bermanfaat.

[Call Friends]

Halo teman teman, untuk mendukung agar saya tetap bisa membuat tulisan-tulisan menarik lainnya. Kamu bisa support saya dengan membeli produk-produk asli produksi sendiri, homemade, dan yang pastinya brand lokal hanya di [@beneteen](https://www.instagram.com/beneteen/) atau ke [beneteen.com](https://beneteen.com/)