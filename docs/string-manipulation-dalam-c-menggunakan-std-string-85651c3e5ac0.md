# String Manipulation dalam C++ Menggunakan std:string

> 原文：<https://medium.easyread.co/string-manipulation-dalam-c-menggunakan-std-string-85651c3e5ac0?source=collection_archive---------1----------------------->

## Apa itu string manipulation dan cara penggunaannya?

![](img/1d9f25967846738ef6bb28d2b33e0f4a.png)

[https://zelig880.com/javascript-string-ecmascript-standards](https://zelig880.com/javascript-string-ecmascript-standards)

Manipulasi string merupakan salah satu hal yang paling sering diujikan dalam kompetisi *competitive programming* . Dalam bahasa pemrograman C, kita mengenal string sebagai kumpulan dari karakter ( *array of char* ) yang diakhiri oleh null terminator (‘\0’). String di dalam bahasa pemrograman C tidak dikenal sebagai sebuah tipe data string, melainkan sebuah tipe data yang disebut array. Kita bisa menggunakan C-Style string untuk memanipulasi string. Berbeda dengan C++. Pada bahasa pemrograman C++, kita bisa menggunakan C-Style dan std:String (pada [artikel sebelumnya](https://medium.com/easyread/perlukah-migrasi-dari-c-ke-c-5b3deb02c64) dikatakan bahwa semua fitur dalam bahasa C bisa kita pakai dalam C++). *Nah,* std:String sendiri merupakan evolusi dari C-Style.

Banyak hal yang sulit, bahkan tidak bisa dilakukan dalam memanipulasi string dengan C-Style, namun kita bisa melakukannya dengan mudah saat menggunakan std:string. Salah satu contoh, yaitu ketika kita ingin menginisialisasi dan menyalin isi dari suatu variabel berisi text ke variabel lain, misalnya:

```
**char nama[10];
nama = “Yosepri DB”;**
```

Hal diatas akan menyebabkan error. Untuk menyimpan string ke dalam sebuah variabel, kita harus menggunakan bantuan fungsi yang ada pada library string.h menjadi seperti :

```
**strcpy(nama, “Yosepri DB”);**
```

Jika kita bandingkan dengan C++, maka proses di atas akan relatif menjadi lebih mudah, yaitu menggunakan `std:string` seperti:

```
**string nama;
nama = “Yosepri”;**
```

Kita dapat melihat perbedaan yang cukup signifikan.

Pada artikel kali ini, saya akan membahas beberapa operasi yang sering dipakai dimulai dari pendeklarasian variabel dan penggunaan fungsi-fungsi dalam memanipulasi string dan saya akan membandingkan bagaimana operasi string tersebut dilakukan apabila menggunakan C-Style.

## **Pendeklarasian Variabel**

Pada C++, pendeklarasian variabel bertipe string cukup mudah. Pendeklarasiannya sama seperti pendeklarasian variabel bertipe int, char, float, dll. Kita dapat mendeklarasikan variabel bertipe string dengan cara:

```
**string nama_variabel;**
```

Pada C-Style,kita harus menyertakan berapa perkiraan ukuran maksimal string yang akan ditampung oleh variabel yang kita buat. Ada beberapa cara. Salah satu caranya yaitu:

```
**char nama_variabel[50];**
```

Namun terdapat masalah jika kita menggunakan cara pendeklarasian diatas. Apabila panjang string yang diinisialisasi lebih besar dibandingkan besar array, maka program akan menghasilkan error.

## **Inisialisasi Nilai**

Proses penginisialisasi nilai kedalam variabel bertipe string dalam std:string cukup mudah. Caranya sama dengan penginisialisasi nilai ke dalam variabel bertipe int, char, dan variabel bertipe lainnya, yaitu seperti:

```
**string nama;
nama = “Yosepri Disyandro Berutu”;**
```

Pada C-Style, kita memerlukan fungsi untuk menginisialisasi nilai yang terdapat dalam library string.h yaitu strcpy. Prinsip penginisialisasian ini sama dengan prinsip penyalinan nilai dari variabel bertipe string dari satu variabel ke variabel yang lain, contohnya :

```
**strcpy(nama, “Yosepri Disyandro Berutu”);**
```

Parameter pertama merupakan tujuan penyimpanan string yang ingin di- *copy* dan parameter kedua merupakan nilai yang akan kita assign.

## **Menggabungkan String**

Dalam `**std:string**` , penggabungan string cukup dengan menambahkan tanda ‘+’, sama seperti penambahan 2 buah bilangan. Misal ada 2 variabel bertipe string yaitu str1 dan str2\. Kemudian kita ingin assign hasil gabungan kedua string tersebut ke sebuah variabel bertipe string yang bernama str_temp. Kita cukup membuatnya menjadi :

```
**str_temp = str1 + str2;**
```

Cukup berbeda dengan menggunakan C-Style. Dalam C-Style, kita menggunakan fungsi strcpy. Prosesnya juga lebih dari satu kali, yaitu:

```
**strcpy(str1, str2);
strcpy(str_temp, str1);**
```

## **Membandingkan String**

Dalam `**std:string**` , kita bisa membandingkan 2 buah string sama seperti tipe data lain.

```
**bool stat = (str1 == str2);**
```

Apabila str1 dan str2 mengandung *value* yang sama, maka stat akan bernilai true. Apabila salah, maka akan berisi false.

## **Mengambil Substring**

Dalam mengambil substring dalam `**std:string**` , kita menggunakan fungsi substr. Fungsi ini mengembalikan nilai string. Misalnya :

```
**string deskripsi = “Saya adalah mahasiswa diploma 3 teknologi informasi di Institut Teknologi Del”;****string sub = deksripsi.substr(12, 9);**
```

Variabel sub akan bernilai ‘mahasiswa’. Parameter pertama berisi posisi karakter pertama yang ingin kita ambil. Parameter kedua merupakan panjang substring yang ingin kita ambil.

## **Menghapus Substring**

Dalam menghapus string, kita menggunakan fungsi erase. Fungsi ini memerlukan 2 buah parameter. Parameter pada fungsi erase sama dengan parameter yang ada dalam fungsi substr. Parameter pertama yaitu posisi string yang ingin kita hapus. Parameter kedua yaitu panjang string yang ingin kita hapus. Contoh nya yaitu:

```
**string nama = “Yosepri Di Berutu”;
nama = nama.erase(8, 3);**
```

maka nama akan menjadi “Yosepri Berutu”.

## **Me- *replace* Substring**

Terkadang kita ingin mengganti substring dalam sebuah string dengan string yang lain. Pada C-Style, hal itu cukup sulit dilakukan. Dengan `**std:string**` , kita cukup menggunakan fungsi replace. Fungsi ini memerlukan 3 buah parameter. Parameter pertama merupakan posisi awal string yang ingin kita replace. Parameter kedua merupakan panjang string yang ingin kita replace. Parameter ketiga merupakan nilai string baru yang akan menggantikan substring yang akan kita replace. Contohnya:

```
**str = str.replace(19, 7, “Del”); //str = “Institut Teknologi Del”.**
```

## **Menukar String**

Dalam menukar string, kita bisa menggunakan fungsi swap. Contoh:

```
**string s1 = “Yosepri”;
string s2 = “Berutu”;****s1.swap(s2);// bisa juga menggunakan swap(s1,s2);**
```

## **Fungsi-fungsi Lain**

```
**string str = “Institut Teknologi Del Tercinta”;****str.size() ; //mengembalikan panjang dari str****str.length(); //mengembalikan panjang dari str****str.max_size(); // mengembalikan panjang maksimal dari str****str.clear();  //mengosongkan string****str.empty(); //cek apakah string kosong. Mengembalikan Boolean****str[i]; //mengembalikan karakter dengan indeks ke-i****str.at(i); // mengembalikan karakter dengan indeks ke-i****str.push_back(‘a’); //menambah karakter a pada akhir str****str.front(); // mengakses elemen pertama dari str****str.back(); //mengakses elemen terkahir dari str**
```

Demikianlah beberapa fungsi dan operasi yang biasanya digunakan dalam memanipulasi string. Secara keseluruhan, saya lebih menyukai manipulasi string dengan menggunakan `**std:string**` dibandingkan dengan menggunakan C-Style String. Semoga bermanfaat!

> Talk less, > more!