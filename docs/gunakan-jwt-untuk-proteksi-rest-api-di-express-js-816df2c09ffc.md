# Menggunakan JWT untuk proteksi REST API di Express JS

> 原文：<https://medium.easyread.co/gunakan-jwt-untuk-proteksi-rest-api-di-express-js-816df2c09ffc?source=collection_archive---------0----------------------->

![](img/5b8d223be7f2334ed4b846098b6bf026.png)

Photo by [Sylwia Bartyzel](https://unsplash.com/photos/7_cSSarxoAA?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/search/photos/padlock?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

Dalam pembuatan aplikasi/web modern, kita pasti tidak akan terlepas dari penggunaan **REST API** . Tapi apakah **REST API** itu ?

**REST** merupakan singkatan dari ***REpresentational State Transfer*** *.* Secara singkat **REST** adalah cara kita untuk menggunakan *resource* (fungsi) yang ada di sebuah server dengan mengakses url yang telah disediakan.

Cara mengaksesnya tentu dengan menggunakan **HTTP** ( *Hyper Text Transfer Protocol* ) dengan *method* ( *http verb* ) yang umum digunakan yaitu:

*   **GET** , untuk membaca *resource* (data).
*   **POST** , untuk membuat *resource* baru (data baru).
*   **DELETE** , tentu untuk menghapus *resource* (data).
*   **PUT** , untuk merubah *resource* (data).

Dan yang perlu diingat ialah bahwa **REST** ini adalah stateless, artinya tidak ada state di dalamnya. Misalnya tidak ada penggunaan *session* . Karena sifatnya, klien hanya meminta ke server dan server akan memberikan responsenya. **titik** . Hanya sampai situ saja. Sehingga untuk proses autentikasi, kita tidak dapat menggunakan session.

Satu hal yang sangat perlu di perhatikan ketika membuat **REST API** adalah *security-* nya. Jangan sampai user yang tidak memiliki otentikasi dan otorisasi dapat menggunakan **REST API** yang kita sediakan. Makadari itu kita memerlukan ***JWT*** , yaitu ***Json Web Token*** . ***Json Web Token*** adalah cara untuk mengautentikasi **REST API** , sehingga hanya orang yang memiliki token saja yang boleh menggunakannya.

Untuk tulisan kali ini, saya sudah menyiapkan aplikasi jadinya, silahkan teman-teman mendownload [disini](https://github.com/haidarafif0809/express-rest-api.git) .

Cara kerja nya sangat sederhana.

**Pertama** kita lakukan *sign* dengan kode berikut, yang akan menghasilkan sebuah token ( *encoded string* ).

```
const jwt = require('jsonwebtoken');const token = jwt.sign({ id: user.id, role: user.role }, 'secret_key');
```

Dalam *sign* token, kita bisa memasukkan berbagai data yang diperlukan seperti userId, userRole. Tapi tidak di sarakan untuk meng- *assign* password. Masukkan seperlunya saja.

Setelah user mendapatkan tokennya, maka token itu akan terus digunakan untuk mengakses berbagai **REST API** yang memerlukan autentikasi token di aplikasi kita. Tapi selama belum *expired* ya, jika sudah *expire* maka user harus melakukan sign kembali untuk mendapatkan kembali token yang baru.

**Kedua** , setiap kali klien mengakses url **REST API** kita, maka kita harus melakukan pengecekan terhadap token yang diberikan. Jika token yang di berikan valid, maka klien diperbolehkan untuk mengakses, jika tidak, maka balas dengan pesan error.

```
// invalid token - synchronoustry {var decoded = jwt.verify(token, 'secret_key');} catch(err) {// err}
```

Setelah verifikasi berhasil, maka hasilnya akan mengembalikan nilai yang kita assign saat di proses pertama (sign).

Dan untuk **secret_key** di atas tadi, terserah mau memasukkan string apa, tapi value antara yang di *sign* dan *verify* harus sama. Dan biasanya saya menggunakan bantuan [dotenv](https://www.npmjs.com/package/dotenv) , untuk memasukkan nilai secret_key, agar lebih aman.

Dalam express, untuk pengecekan nya bisa di lakukan dengan middleware. Jika kamu belum paham dengan middleware, saya akan jelaskan di artikel berikutnya.

*Jika anda merasa artikel ini menarik dan bermanfaat, silahkan* ***berikan claps*** *👏 👏 sebanyak-sebanyaknya dan* ***bagikan*** *ke lingkaran pertemanan anda, agar mereka dapat membaca artikel ini. Dan jangan lupa* [***follow saya di medium***](https://medium.com/@haidarafifmaulana) *untuk terus dapatkan tulisan seperti ini setiap minggunya.*