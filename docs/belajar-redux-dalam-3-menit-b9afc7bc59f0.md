# Belajar Redux dalam 3 Menit

> 原文：<https://medium.easyread.co/belajar-redux-dalam-3-menit-b9afc7bc59f0?source=collection_archive---------0----------------------->

![](img/ba2dbb3028bd35ce8f94d790254da795.png)

Photo by [Glen Noble](https://unsplash.com/@glennoble?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

S tate merupakan salah satu fitur unggulan yang ada di *library front-end modern* ***Jaman Now*** yang biasa digunakan untuk ***SPA* ( *Single Page application* )** . Baik itu di *vuejs* , *reactjs* dan kawan-kawannya, pasti menggunakan *state* untuk manajemen data di komponennya.

Masih baru denger tentang *vue* ? Boleh datang ke [artikel ini](https://medium.com/easyread/reaksi-pertama-saat-coba-vue-js-4c2c1fbbd75a) .

[](https://medium.com/easyread/reaksi-pertama-saat-coba-vue-js-4c2c1fbbd75a) [## Reaksi Pertama saat coba Vue js

### Sampai beberapa waktu yang lalu saya gak ngerti kenapa javascript bisa jadi bahasa pemrograman no 1 paling banyak di…

medium.com](https://medium.com/easyread/reaksi-pertama-saat-coba-vue-js-4c2c1fbbd75a) 

Salah satu masalah yang dihadapi ketika membuat aplikasi SPA adalah mengolah *state* (data). Karena satu *state* yang ada di suatu komponen tidak bisa diambil oleh komponen lainnya.

Misal kita punya state `**isLogin**` di komponen `**Home**` , ketika kita ingin megakses state `**isLogin**` itu pada komponen `**About**` maka tidak akan bisa.

Karena hanya satu *state* yang dapat hidup dimana *state* itu didefiniskan. Untuk itu lah hadir ***Redux*** sebagai state management. Dengan menggunakan *Redux* maka kita cukup bikin satu *state* dan *state* itu bisa di akses di komponen manapun.

Dan untuk pembelajaran kali ini kita hanya akan menggunakan pure *Redux* saja, dan perlu di ketahui juga *redux* ini sering dipasangkan dengan *react* . Padahal sebenarnya *redux* ini bukan punyanya *react* . Kita bisa menggunakan *redux* di manapun, baik itu *vuejs* , *angular* dan kawan-kawannya.

Untuk mulai menggunakan *redux* , tentu saja kita harus menginstallnya

```
$ npm install redux // atau$ yarn add redux
```

Ada 3 poin penting dalam redux :
- **Store
- Reducer
- Action**

Kita mulai dari **Action.** Sederhananya sebuah *action* merupakan sebuah *object* yang memiliki *property* *type* .

```
const ADD_DATA  = { type: 'ADD_DATA' }
```

yang mana *object* *action* ini nantinya akan dikirim ke **Store** dengan cara `store.dispatch(ADD_DATA)` , untuk kemudian nanti di olah oleh **Reducer.**

Jika di perlukan, *Action* bisa menampung data dengan cara menambahkan *property* kedua dalam *object* *Action* -nya.

```
const ADD_DATA = { type: 'ADD_DATA', data: 'Ini Data Baru' }
```

Untuk nama *property* keduanya bebas. Tapi untuk *property* `type` tidak boleh diganti. Atau agar data bisa lebih dinamis, kita bisa membuat function yang mengembalikan *object action* .

```
const ADD_DATA = (newData) => { return { type: 'ADD_DATA', data: newData }}export default ADD_DATA
```

Jika aplikasi kita sudah semakin besar, maka saat nya untuk memisahkan *Action* ke file yang terpisah.

Cukup untuk *Action* , lanjut ke **Reducer** .

*Reducer* adalah bagian *redux* yang merubah *state* menjadi respon yang terjadi ketika *Action* di `dispatch()` .

Jadi bisa dibilang, untuk merubah *state* hanya bisa dilakukan di Reducer, dan *Reducer* hanya melakukan perubahan *state* jika ada *action* yang di `dispatch()` . Secara sederhana *reducer* ini hanya sebuah *function* yang mengembalikan *state* baru.

```
const initialState = { }const reducer = (prevState = initialState, action) => {
  if (action.type === 'ADD_DATA') {
    return { ...prevState, action.data }
  } return prevState
}export default reducer
```

Jadi yang terjadi adalah jika *action* yang di `dispatch()` *type* nya adalah `ADD_DATA` maka akan mengembalikan sebuah *object* baru yang isinya adalah *state* lama di tambah dengan data baru yang di *passing* melalui *Action* **.** Hasil dari *reducer* inilah yang akan menjadi *state* yang baru.

Cukup untuk *Reducer* , kita lanjut ke ***Store*** .

Tugas *Store* adalah menggabungkan *Action* dan *Reducer* agar bisa bekerja sebagai state manajemen.

Store bertanggung jawab sebagai:
- menyimpan keseluruhan *state* .
- mengakses *state* dengan cara `getState()` -menjalankan *reducer* untuk merubah *state* dengan cara `dispatch(action)` .

Bagaimana kode nya ?

Bagaimana cara mengakses *state* nya ?

```
import store from './store'store.getState()
```

Bagaimana cara merubah *state* ?

```
import ADD_DATA from './action'
import store from './store'store.dispatch(ADD_DATA('data baru'))
```

Menurut saya penjelasan di atas sudah cukup untuk bisa memahami pondasi *redux* . Karena *redux* hanya state manajemen, ya berarti hanya berurusan dengan manipulasi *state* saja.

Jika ada bagian yang kurang jelas, silahkan tanyakan melalui kolom response di bawah ya 😄

Untuk selanjut nya, kita akan bahas bagaimana menggunakan redux di react dengan `react-redux` .

*Jika anda merasa artikel ini menarik dan bermanfaat, silahkan* ***berikan claps*** *👏 👏 sebanyak-sebanyaknya dan* ***bagikan*** *ke lingkaran pertemanan anda, agar mereka dapat membaca artikel ini. Dan jangan lupa* [***follow saya di medium***](https://medium.com/@haidarafifmaulana) *untuk terus dapatkan tulisan seperti ini setiap minggunya.*