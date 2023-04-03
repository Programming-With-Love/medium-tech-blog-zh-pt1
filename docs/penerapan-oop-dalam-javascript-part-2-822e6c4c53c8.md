# Penerapan OOP dalam Javascript (Part 2)

> 原文：<https://medium.easyread.co/penerapan-oop-dalam-javascript-part-2-822e6c4c53c8?source=collection_archive---------4----------------------->

## Getter dan Setter

![](img/b028197d5934445236a723d621cc912b.png)

Photo by [Craig McLachlan](https://unsplash.com/@crrrrraig?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Setelah pada artikel sebelumnya kita membahas mengenai [***constructor, property, class method dan static method***](https://medium.com/easyread/penerapan-oop-dalam-javascript-part-1-98ed3a427e77) . Kali ini sebagai lanjutan kita akan membahas mengenai ***getter*** dan ***setter*** .

[](https://medium.com/easyread/penerapan-oop-dalam-javascript-part-1-98ed3a427e77) [## Penerapan OOP dalam Javascript (Part 1)

### OOP atau Object Oriented Programming merupakan salah satu pattern pada programming yang sangat umum digunakan oleh…

medium.com](https://medium.com/easyread/penerapan-oop-dalam-javascript-part-1-98ed3a427e77) 

Di bawah ini adalah contoh *script* sebelumnya yang **tidak** menggunakan *getter* dan *setter* .

*Nah* , sedangkan kode dibawah ini contoh script ketika **sudah** menggunakan *getter* dan *setter* .

Oke, kita mulai dari *getter* ***.***

Dengan menggunakan *getter* maka kita tidak akan mengakses data raw secara langsung dari *property* yang ingin kita akses, melainkan melalui sebuah *method getter* yang kan mengambil dan mengolah data nya sebelum di *return* . Seperti namanya, *getter* , artinya mengambil.

Getter mengambil data dari *property* asli lalu di *return* atau bisa juga sebelum di *return* , datanya di olah terlebih dahulu, misalnya melakukan pengecekan `null` atau `tidak null` .

Seperti contoh script ***get color*** di atas. Sebelum data di *return* , *property* `**this._color**` di olah lebih dahulu dengan menambahkan beberapa *text* di depannya. Maka ketika kita memanggil `**car.color**` pada contoh di atas maka akan mengembalikan *string* dengan nilai `Warna Mobilnya Adalah Merah`

Selesai dengan *getter* , langsung ke ***setter.***

*Setter* merupakan kebalikan dari *getter* yang mengambil nilai property. *Setter* digunakan untuk meng- *assign* nilai ke *property* . Jika kita ingin agar data di olah terlebih dahulu sebelum di masukkan ke property aslinya, maka kita membutuhkan *setter* .

Seperti contoh *script* di atas, sebelum data `**merah**` di masukkan ke `**this._color**` maka di tambahkan dulu string `**keren**` di belakangnya baru di assign ke `**this._color**` .

Secara sederhana, sebenarnya *getter* dan *setter* hanya seperti itu, jadi kalau kita mau data dari *property* diolah dulu sebelum di tampilkan maka gunakan *getter* dan jika data ingin di olah dulu sebelum di *assign* ke property maka gunakan *setter* . Gunakan dengan bijak hehe.

*Jika anda merasa artikel ini menarik dan bermanfaat, silahkan* ***berikan claps*** *👏 👏 sebanyak-sebanyaknya dan* ***bagikan*** *ke lingkaran pertemanan anda, agar mereka dapat membaca artikel ini. Dan jangan lupa* [***follow saya di medium***](https://medium.com/@haidarafifmaulana) *untuk terus dapatkan tulisan seperti ini setiap minggunya.*