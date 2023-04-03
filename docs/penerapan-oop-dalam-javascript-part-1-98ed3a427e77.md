# Penerapan OOP dalam Javascript (Part 1)

> 原文：<https://medium.easyread.co/penerapan-oop-dalam-javascript-part-1-98ed3a427e77?source=collection_archive---------0----------------------->

![](img/f4180d8e38f1f44746f58795d73321ca.png)

Photo by [Sean Whelan](https://unsplash.com/@sswhelan?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

**OOP** atau ***Object Oriented Programming*** merupakan salah satu *pattern* pada *programming* yang sangat umum digunakan oleh *developer* di dunia. Kalau berbicara mengenai *OOP* pasti kaitannya sangat erat dengan bahasa pemograman Java. *Ya* secara bahasa Java itu adalah bahasa yang *pure* mengusung *pattern OOP* .

*Nah* , Javascript sebagai bahasa yang terpopuler didunia saat ini tentu saja tidak mau kalah dong. Karena sekarang pada Javascript kita juga bisa menerapkan *pattern OOP* .

Mau tahu gimana penerapan *pattern OOP* pada Javascript ? Simak tulisan ini sampai habis ya! 😄

## Object Literal VS Object Class

Di Javascript ada tipe data yaitu *object literal* . *Ya* dia juga berupa *object* . Contoh kodenya seperti ini.

tetapi kita tidak akan membahas mengenai *object literal* , melainkan kita akan membahas mengenai *object class* . Walaupun dua-duanya adalah *object* , tetapi keduanya memiliki perbedaan.

Classes Javascript

Sebelum kita memiliki sebuah *object* , maka kita perlu cetakan untuk membuat *object-* nya. *Nah* cetakan itu disebut ***class* .** Sama seperti kita ingin membuat mobil, maka kita perlu cetakan atau *blue print* untuk membuat mobilnya.

Di dalam kelas ada beberapa attribut penting di dalamnya yaitu:
- Constuctor
- Property
- Class Method
- Static Method
- Getter & Setter
- Inheritance

Mari kita bahas satu persatu.

## Constuctor

Adalah sebuah *method* / *function* yang di jalankan pertama kali ketika *object* di buat. Misalnya seperti contoh kode di atas ketika `const = new Car('suv', 'red')` di jalankan maka method `constructor` langsung di jalankan juga, yang isinya adalah memasukkan parameter `suv` dan `red` ke dalam *property* car `this.type` dan `this.color` .

## Property

Adalah data yang di simpan di dalam sebuah *object* . Pada contoh di atas adalah `this.type` dan `this.color` , *property* dapat diakses di *class method* manapun yang ada di dalam *object* . Di atas saya memasukkan string `on` ke dalam `this.engineStatus` di dalam method `engineStart()`

## Class Method

Adalah *method* / *function* yang ada di dalam sebuah *object* , dan untuk menggunakannya *class* harus di *instance* terlebih dahulu menjadi *object* baru bisa dijalankan. Contoh *class* mobil di atas, kita akan menggunakan *method* `engineStart` .

```
const car = new Car('suv', 'red')car.engineStart()
```

## Static Method

Adalah *method* yang sama seperti *class* *method* , tetapi untuk menjalankannya tidak perlu meng *instance* class, cukup menggunakan `NamaClass.namaMehod()`

```
const car = new Car('suv', 'red)Car.isEngineOn(car) // Engine Offcar.engineStart()Car.isEngineOn(car) // Engine On
```

Mungkin cukup dulu sampai sini untuk part 1\. Untuk part 2 nanti kita akan membahas lebih jauh mengenai getter dan setter, dan untuk part 3 nya yaitu tentang inheritance.

Gak usah panjang-panjang tutorialnya, biar enak belajar nya hehe.

*Jika anda merasa artikel ini menarik dan bermanfaat, silahkan* ***berikan claps*** *👏 👏 sebanyak-sebanyaknya dan* ***bagikan*** *ke lingkaran pertemanan anda, agar mereka dapat membaca artikel ini. Dan jangan lupa* [***follow saya di medium***](https://medium.com/@haidarafifmaulana) *untuk terus dapatkan tulisan seperti ini setiap minggunya.*