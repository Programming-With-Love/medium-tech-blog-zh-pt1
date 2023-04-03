# Today I Learned Series: Keunikan Kurung Kurawal pada Golang

> 原文：<https://medium.easyread.co/today-i-learned-series-keunikan-kurung-kurawal-pada-golang-7cdb4f8881c0?source=collection_archive---------0----------------------->

## Part of [Today I Learned Series](https://medium.com/easyread/today-i-learned/home) in Easyread

![](img/a8da4c93b8c31910945541ac7b853aa0.png)

Today I Learned From Google Image Search

Hari ini saya membaca sebuah *joke* yang cukup lama dan bisa dibilang sudah basi. Tetapi *joke* tersebut tetap dapat membuat saya tertawa ketika melihatnya kembali. *Joke* tersebut bercerita tentang 2 jenis tipe *programmer*

```
if (K_RStyle) {
   //statement
}
```

atau

```
if (AllmanStyle)
{
     //statement
}
```

*Nah* , maksud dari potongan kode di atas ialah menggambarkan 2 jenis *programmer* tadi, yaitu

*   **Tipe K_R Style (** Kernighan & Ritchie Style)
    *Programmer* yang menempatkan kurung kurawal langsung di samping setelah *conditional statement,* seperti *if-else, for, while* , dan sebagainya. *Style* ini diperkenalkan oleh [Kernighan dan Ritchie](https://en.wikipedia.org/wiki/C_(programming_language)#K&R_C) pada buku [The C Programming Language](https://en.wikipedia.org/wiki/The_C_Programming_Language) .
*   **Tipe Allman Style**
    *Programmer* yang menempatkan kurung kurawal di bawah setelah *conditional statement* atau boleh dikatakan meletakkannya pada baris baru setelah menekan enter. *Style* ini dipopulerkan oleh [Eric Allman](https://en.wikipedia.org/wiki/Eric_Allman) dikarenakan beliau sering menulis *plugin dan utilities* pada [BSD](https://en.wikipedia.org/wiki/Berkeley_Software_Distribution) .

Karena 2 tipe *programmer* yang diceritakan pada *joke* tersebut, tidak sedikit programmer yang berdebat hanya karena masalah penempatan kurung kurawal ini. Bahkan banyak pula yang menanggapi *joke* ini dengan serius.

Setelah melihat *joke* tadi, saya lalu melihat kodingan saya dan ternyata hampir semua kode yang saya tulis menggunakan *style* **K_RStyle** atau bisa dikatakan saya adalah *programmer* tipe pertama 😈.

## Kurung Kurawal pada Golang

Selain itu setelah melihat struktur kodingan *Golang* , saya baru menyadari bahwa ternyata paradigma 2 jenis *programmer* ini secara langsung dipotong dan dipatahkan oleh *Google* sendiri selaku pencipta *Golang. Golang* sendiri hanya mendukung **K_RStyle** . Hal ini dibuktikan dengan percobaan saya menulis kode dengan **Allman** **Style** dan alhasil akan menghasilkan *error* pada saat *compilation.*

Mungkin mereka ingin mengurangi konflik *diversity* yang terjadi melalui struktur *Golang* itu sendiri. Atau mungkin saja *style* ini mempengaruhi *performance* kodingan ? Saya pun akhirnya penasaran mengapa Google hanya memilih **Tipe K_RStyle** pada *Golang* .

Setelah mencari ke beberapa sumber, saya akhirnya menemukan alasan mengapa Google memilih menggunakan **K_RStyle** pada *Golang* **.** Seperti yang disebutkan pada dokumentasi *Golang*

> Since Go code is meant to be formatted automatically by `[gofmt](https://golang.org/cmd/gofmt/)` , some style must be chosen ~ [https://golang.org/doc/faq#semicolons](https://golang.org/doc/faq#semicolons)

Selain itu juga dijelaskan bahwa pada *Golang* , kurung kurawal digunakan untuk pengelompokan *statement* , berbeda dengan bahasa pemograman lain yang sejenis misalnya *Java* .

```
class HelloWorldApp {
    public static void main(String[] args) {
        System.out.println("Hello World!"); **// Statement 1** int a = 0; // **Statement 2**
        int b = 0; // **Statement 3** }
}
```

Jika di lihat pada potongan kode *Java* diatas, group *statement* di tandai dengan titik koma `;` . Dan titik koma `;` akan digunakan saat menerjemahkan bahasa pemograman ke bahasa mesin.

Berlandaskan hal tersebut, author *Golang* sepakat untuk setiap *statement* pada kodingan *Golang* akan langsung otomatis di- *inject* dengan titik koma `;` langsung saat diterjemahkan ke bahasa mesin. Namun secara tidak langsung mereka akan menghapus titik koma dari interface atau saat menulis kodingan, karena titik koma adalah bahasa mesin bukan bahasa manusia, jadi titik koma tidak perlu untuk ditampilkan kepada manusia, namun di- *inject* langsung saat kompiling untuk di baca mesin.

> To achieve this goal, Go borrows a trick from BCPL: the semicolons that separate statements are in the formal grammar but are injected automatically, without lookahead, by the lexer at the end of any line that could be the end of a statement. ~ [https://golang.org/doc/faq#semicolons](https://golang.org/doc/faq#semicolons)

Sehingga saat kita membuat kodingan seperti berikut :

```
if a== true
{
 // others
}
```

Berdasarkan penjelasan diatas kompiler *Golang* secara otomatis akan menambahkan titik koma `;` pada akhir *conditional statement* menjadi:

```
if a == true ;
{
}
```

Dan inilah hal yang salah yang menyebabkan kegagalan proses kompilasi yang menghasilkan *error* . Untuk mengatasi hal tersebut akhirnya *Golang* memilih **K_RStyle** dan membuat *tools* `gofmt` , dimana *tools* tersebut berfungsi untuk memperbaiki dan me- *format* kodingan golang secara otomatis.

Itulah alasan mengapa *Golang* memilih memakai **K_RStyle** (Kernighan & Ritchie Style) dibangingkan **Allman Style** .

Referensi:

*   [https://golang.org/doc/faq#semicolons](https://golang.org/doc/faq#semicolons)