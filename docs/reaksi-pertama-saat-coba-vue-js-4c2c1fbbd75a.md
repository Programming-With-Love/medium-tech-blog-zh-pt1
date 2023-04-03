# Reaksi Pertama saat coba Vue js

> 原文：<https://medium.easyread.co/reaksi-pertama-saat-coba-vue-js-4c2c1fbbd75a?source=collection_archive---------0----------------------->

![](img/51a3821c58381357a56385d5332ff661.png)

Vue js

Sampai beberapa waktu yang lalu saya gak ngerti kenapa javascript bisa jadi bahasa pemrograman no 1 paling banyak di gunakan di dunia. Padahal javascript itu kan setahu saya digunakan untuk membuat website lebih interaktif.

Googling-googling sampai kemudian baru nyadar ada banyak banget framework dan library keren yang dibuat dengan javascript.

(Singkatin aja ya javascript jadi js)

Ada react, angular, node js , react native , electron , keren-keren banget. Langsung mau di pelajari semua, greget banget haha.

Sampai kemudian bertemu dengan vue js. Pertama ketemu vue js ini , saat lagi sibuk ngulik laravel. Kebeteluan lagi garap project market place untuk warung-warung [war-mart.id](https://war-mart.id) , bingung banget pake framework apa ya untuk nanganin front end nya , sudah pernah buat POS app dengan jquery ribet nya ampun.

Dan kerennya lagi dalam laravel sudah termasuk di dalamnya vue js . jadi ketika install laravel , otomatis di dalamnya sudah terdapat vue js .

manfaat utama yang saya rasakan ketika menggunakan vue js , kita bisa lebih fokus kepada masalah logic bisnis aplikasi, ketimbang fokus untuk DOM manipulation, css manipulation dan sbg.

di artikel ini kita akan buat simple app kalkulator dengan laravel dan vue js.

oke pertama install laravel

```
composer create-project laravel/laravel --prefer-dist kalc-vue
```

masuk ke direktori nya, lalu jalankan command berikut :

```
npm install
```

untuk menjalankan command di atas memerlukan node js dan npm.

oke sejauh ini kita sudah berhasil menginstall laravel dan vue js di dalamnya .

lalu dimana letak kode vue js kita ? ada di direktori resources -> assets -> js

![](img/3e60668ca380a8db63ea774de2bb3da7.png)![](img/f38d8cd8d94aed9293e1497bc392bb30.png)

resource/asset/js/app.js

bagaimana menjalankan nya di laravel ?

untuk contoh , kita buat satu view yang akan menjalankan component example. apa itu component ? silahkan simak di bawah.

buat route nya di routes/web.php

![](img/14abb1b288e6f6b1a349961f8ceef728.png)

buat view nya di resources/views/ dengan nama vue.blade.php , lalu ketikkan kode berikut :

![](img/173ef45f40d8ac1dfa6a47649d24847e.png)

vue.blade.php

hasilnya kita jalankan di browser [http://localhost/kalc-vue/public/vue](http://localhost/kalc-vue/public/vue) :

![](img/d0f7f3cc63634193c45662e787fbac01.png)

Example Component

jika kita perhatikan kode di atas terdapat tag <example>, itu bukan tag bawaannya html , itu adalah custom component fitur keren punya vue js.</example>

untuk membuat component, laravel sudah menyediakan satu folder bernama components di resources/assets/js/components . semua component yang kita buat di taruh di sana .

![](img/a645c116e7f524cff057080c96f84f2e.png)

Component Directory

nah , component example itu ada di file Example.vue .

![](img/92102b45bd2fbbe62c3832ab7cda19cd.png)

Example.vue

sekarang coba kita edit Example.vue

![](img/154cf940ced13f70bc72527cd9019df7.png)

cek lagi hasilnya di browser dengan merefresh halamannya, hasilnya akan tetap sama seperti sebelum di edit.

![](img/5fe79513719efa7d61ca14321f578fb5.png)

kenapa ya seperti itu? seharusnya kan ikut berubah. itu karena app.js yang ada di public/js/app.js dengan yang ada di resource/assets/js/app.js , tidak akan otomatis berubah jika kita melakukan edit di folder resources/assets/js

bagaimana cara nya supaya otomatis tersinkronisasi ? kita gunakan **watch.**

```
npm run watch 
```

jalankan command di atas di direktori laravel app kita dan jangan pernah di tutup selama masih melakukan editing di folder /resources/asset/js .

dengan menjalankan command di atas maka setiap edit yang terjadi di folder resources/assets/js akan tersinkronisasi dengan public/js/ .

kita lihat lagi hasilnya di browser maka seharusnya sudah berubah sesuai dengan edit yang dilakukan.

![](img/8ed2c613bbea93dc5a2d18b33d0e6ae7.png)

jika saat di refresh tidak berubah juga , silahkan coba force refresh agar file app.js nya di cache ulang oleh browser.

oke, cukup untuk contoh Example.vue nya , kita akan lanjut buat simple calculator .

pertama, buat dulu route nya di routes/web.php

![](img/4e281867e4fecc555df561eedbeb2939.png)

lalu kita buat view nya kalkulator.blade.php

![](img/0b8f006fc5300677911c2acfa176d405.png)

lalu kita buat component form kalkulator nya :

![](img/20b6ca185d8fae83e105d6a79c129448.png)

lalu isi dengan kode berikut :

```
<template>
 <div class="container"><form  class="text-center" style="margin-top: 20%">
   <h1 class="text-center">Kalc Vue App</h1>
   <input type="number" v-model.number="angka_1"> +
   <input type="number" v-model.number="angka_2">
  </form>
  <h2 class="text-center">{{ angka_1 + angka_2}}</h2>
 </div>
</template><script>
export default {
 data: function () {
  return {
   angka_1: 0,
   angka_2: 0
  }
 }
}
</script>
```

setelah component berhasil di buat, kita import ke vue lewat app.js , buka app.js lalu edit seperti dibawah ini :

![](img/cd15a89b3077605e2c95238c54fcc7a7.png)

sampai tahap ini koding kita sudah selesai, cek hasilnya di browser [http://localhost/kalc-vue/public/kalkulator](http://localhost/kalc-vue/public/kalkulator)

![](img/deb13f1d738c383f51e484ee6aba97cd.png)

Kalc Vue App

coba ubah angka pertama dan angka kedua nya , maka hasilnya akan otomatis berubah , tanpa kita harus lakukan DOM manipulation secara manual dengan javascript. Keren Banget !

agar pemahaman fundamental vue js anda makin mantap, silahkan baca dokumentasi resmi vue js di [https://vuejs.org/](https://vuejs.org/) , dokumentasi nya sama kerennya dengan hasil kodingnya.

Mungkin cukup sampai sini pembahasan awal tentang vue js.

untuk tutorial selanjutnya saya akan buat tutorial bagaiman membuat crud laravel — vue , termasuk pagination , loading spinner, search data, selectize, swal(sweet alert) di vue js .

See You in the next tutorial .

Happy Coding :)

untuk source code lengkap silahkan kunjungi [github saya](https://github.com/haidarafif0809/kalc-vue) .

*Jika anda merasa artikel ini menarik dan bermanfaat, silahkan* ***berikan claps*** *👏 👏 sebanyak-sebanyaknya dan* ***bagikan*** *ke lingkaran pertemanan anda, agar mereka dapat membaca artikel ini. Dan jangan lupa* [***follow saya di medium***](https://medium.com/@haidarafifmaulana) *untuk terus dapatkan tulisan seperti ini setiap minggunya.*