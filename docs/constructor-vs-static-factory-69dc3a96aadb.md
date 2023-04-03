# Constructor vs Static Factory

> 原文：<https://medium.easyread.co/constructor-vs-static-factory-69dc3a96aadb?source=collection_archive---------3----------------------->

![](img/fd08fe593a616c17f43fbbf350b29ab7.png)

Sumber : [https://jlordiales.wordpress.com/2012/12/26/static-factory-methods-vs-traditional-constructors/](https://jlordiales.wordpress.com/2012/12/26/static-factory-methods-vs-traditional-constructors/)

Ketika mempelajari konsep OOP, biasanya kita akan banyak mendengar kata *konstruktor* . Begitu juga dalam dunia *coding* sendiri, konsep tersebut banyak digunakan. *Nah* , kali ini saya ingin memperkenalkan solusi lain dengan konsep serupa selain menggunakan konstruktor yaitu *static factory* .

Biasanya dengan menggunakan konstruktor kita menggunakan cara seperti berikut.

```
class Color { private final int hex; Color(String rgb) { this(Integer.parseInt(rgb, 16)); } Color(int red, int green, int blue) { this(red << 16 + green << 8 + blue); } Color(int h) { this.hex = h; }}
```

Sedangkan dengan menggunakan *static factory* seperti berikut.

```
class Color { private final int hex; static Color makeFromRGB(String rgb) { return new Color(Integer.parseInt(rgb, 16)); } static Color makeFromPalette(int red, int green, int blue) { return new Color(red << 16 + green << 8 + blue); } static Color makeFromHex(int h) { return new Color(h); } private Color(int h) { return new Color(h); } }
```

# Berikut beberapa perbedaan antara *konstruktor* dan *static factory*

## **Tidak seperti konstruktor static factory mempunyai nama**

Salah satu kemudahan dengan menggunakan *static factory* adalah kita lebih mudah mengenali yang kita panggil karena tersedianya nama. Seperti contoh dibawah ini, kita akan membuat warna merah tomat dan diberi nama *tomato* .

Dengan menggunakan konstruktor.

```
Color tomato = new Color(255, 99, 71);
```

Dengan menggunakan static factory.

```
Color tomato = Color.makeFromPalette(255, 99, 71);
```

## **Tidak seperti konstruktor, kita tidak diharuskan membuat objek baru supaya bisa dipanggil**

Seperti yang sudah dijelaskan pada nomor 1, ketika menggunakan static factory, kita tidak perlu membuat objek baru. Karena metode bersifat *static* maka kita dapat menggunakannya secara langsung.

## **Tidak seperti konstruktor , static factory dapat mengembalikan objek**

Dengan adanya fungsi *return* di static factory, memberikan fleksibilitas dalam memilih objek yang dikembalikan. Salah satu pengaplikasian dalam hal fleksibilitas adalah API. API dapat megembalikan objek tanpa membuat kelasnya *public* .

Demikianlah yang dapat saya bagikan mengenai perbandingan *static factory* dengan *konstruktor* . Jadi bagi kamu yang sering berkutat dengan konsep OOP, kamu boleh mencoba menerapkan *static factory* dan lihat perbedaannya. Terima kasih and ***keep learning!***

## Referensi

[**Constructors or Static Factory Methods?**](https://www.yegor256.com/2017/11/14/static-factory-methods.html) **-** by Joshua Bloch