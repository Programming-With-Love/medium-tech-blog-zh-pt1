# Series — Membuat Data Model dengan Sentuhan Lombok

> 原文：<https://medium.easyread.co/series-membuat-data-model-dengan-sentuhan-lombok-af4a57a75198?source=collection_archive---------2----------------------->

## 4 — Using Lombok

![](img/1b8d67790fc52924b7cbc2316ddbd2f6.png)

Photo by [Saad Salim](https://unsplash.com/@saadx?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/s/photos/construction?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# Rest API dengan Spring Boot — Series List

[**0\. Series — REST API dengan Spring Boot**](https://medium.com/easyread/series-rest-api-dengan-spring-boo-2d74060e69fb)[**1\. Series — Dimulai dari Instalasi!**](https://medium.com/easyread/series-dimulai-dari-instalasi-b564fb981d4)[**2\. Series — Inisiasi Project**](https://medium.com/easyread/series-inisiasi-project-1e37ffa951ed)[**3\. Series — Rancangan dan Konfigurasi Database dengan H2**](https://medium.com/easyread/series-rancangan-dan-konfigurasi-database-dengan-h2-3af60e66e4ef) **4\. Series — Membuat Data Model dengan Sentuhan Lombok — You’re here** [**5\. Series — Menggunakan Spring JPA (1)**](https://medium.com/easyread/series-menggunakan-spring-jpa-1-da3ea1274f7d)[**6\. Series — Menggunakan Spring JPA (2)**](https://medium.com/easyread/series-menggunakan-spring-jpa-2-8673af359e1a)[**7\. Series — Inisialisasi Data**](https://medium.com/easyread/series-inisialisasi-data-aa2ae7d36691)

P ada *part* [sebelumnya](https://medium.com/@amendo_s/database-desain-dan-konfigurasi-database-dengan-h2-3af60e66e4ef) kita telah memiliki desain *database* untuk aplikasi kita. Sekarang, mari kita lanjutkan.

Pertama, kita akan membuat sebuah *package* **domain** yang akan berisi *class* data *model* dari aplikasi kita. Biasanya untuk *class data model* , kita hanya perlu membuat POJO ( *Plain Old Java Object* ).

POJO adalah sebuah *class* yang berdiri sendiri tanpa ada ketergantungan dengan *class* lain (tidak *extends* atau *implements* *interface* ). *Nah* , oleh karena sifat itu, POJO ini biasanya digunakan sebagai *model* dari sebuah data tertentu. *Masih bingung?* Sederhananya, POJO itu adalah *class* yang hanya terdiri dari *variable* , *constructor* dan *getter setter* . Untuk lebih mudah memahami, mari kita praktik langsung.

Mari kita membuat *class* `**Tour**` terlebih dulu. Kemudian di dalam *class* tersebut, tambahkan isi *variable-* nya sesuai dengan yang ada pada desain *class* *diagram* kita. Setelah itu, kita dapat dengan mudah men- *generate* *constructor* dan *getter* *setter-* nya melalui IDE kita ( *buat yang belum tau* ). Kita hanya perlu menekan `**alt + insert**` bersamaan, maka tinggal pilih apa yang mau di- *generate* . Karena kita perlu *constructor* dan *getter* *setter* , silahkan pilih `**Getter Setter**` dan `**Constructor**` . Jika sudah, kita sudah memiliki sebuah POJO.

*Nah* , sekarang teman-teman silahkan buat *class* untuk `**Tour Package, ENUM Region, ENUM Difficulty**` sesuai dengan *class diagram* yang telah kita miliki.

Setelah kita membuat *class* untuk *model* *database* kita, coba teman-teman perhatikan *class* `**Tour**` . *Class* tersebut memiliki *line of code* yang panjang setelah kita men- *generate* *constructor* dan *getter* *setter* -nya. *Auto generate* yang kita gunakan tadi membantu kita dengan mudah membuat *constructor* dan *getter setter* tetapi hasilnya, terdapat *line of code* yang panjang. Bayangkan apabila kita memiliki lebih banyak *variable* yang harus kita buat juga *getter setter-* nya. *And* **lombok** *was here* 🌞.

[**Lombok**](https://www.baeldung.com/intro-to-project-lombok) adalah sebuah *library* yang berguna untuk mengurangi *boilerplate* dari sebuah *code* . *Boilerplate* *simple-* nya adalah *term* yang digunakan untuk menjelaskan sebuah *code* yang digunakan dibanyak tempat dengan sedikit perubahan. Dalam kasus kita, *getter setter* dan *constructor* adalah *boilerplate* kita.

*Boilerplate* yang kita miliki saat ini sangat panjang, jadi agar lebih *simple* kita bisa menggunakan **lombok** . Bagaimana cara menggunakannya?

Untuk menggunakan lombok, kita hanya perlu menggunakan **anotasi** . Coba teman-teman hapus semua *getter setter* yang ada. Kemudian, untuk mengganti *getter setter* tersebut, cukup dengan menambahkan anotasi `**@Getter**` dan `**@Setter**` pada *class* `**Tour**` .

```
**@Getter
@Setter
public class Tour{
      . . .
}**
```

*That’s all* , hanya itu yang perlu kita lakukan. Cukup *simple* kan. Dengan menggunakan anotasi tersebut, apapun *variable* yang akan kita tambahkan, lombok akan otomatis membuat *getter setter-* nya. Teman-teman boleh coba untuk membuktikannya. Buat satu java *class* , tambahkan *method* `**public static void main(){}**` , instansiasi *class* `**Tour**` dan cobalah untuk *set* *varible* yang diinginkan lalu coba *print* ke *console* . *That’s it* . Kita dapat menggunakan *getter setter* kita.

Lombok juga memiliki sebuah anotasi `**@Data**` . Anotasi ini akan secara otomatis memberikan teman-teman *getter setter* dan *required arguments constructor* ( *constructor* yang berisi semua *variable* dari *class* tersebut). Semakin *simple* bukan. Untuk *constructor* sendiri, lombok secara *default* sebenarnya memiliki 3 jenis anotasi untuk *constructor* , yaitu `@**NoArgsConstructor, @RequiredArgsConstructor, @AllArgsConstructor**` . Teman-teman bisa menyesuaikan anotasi mana yang akan digunakan sesuai kebutuhan.

Setelah mengetahui jenis dari anotasi *constructor,* maka kita akan gunakan pada *project* kita dan hasilnya sebagai berikut. Teman-teman tinggal lakukan hal yang sama untuk *class* `**Tour Package**` *.*

Tour.java dengan lombok

Untuk class Enum sendiri, teman — teman tinggal buat seperti ini 👇

Ada sebuah kasus, misalnya kita sudah menggunakan anotasi `**@AllArgsConstructor**` . Apabila kita ingin *create* sebuah *object* , tentu saja kita harus melakukan instansiasi dan *assign* seluruh parameter yang dibutuhkan sesuai *constructor* kita. Tetapi kita ingin membuat sebuah *object* dengan menggunakan properti tertentu saja ( *custom* ). Hal ini sebenarnya bisa kita lakukan secara manual dengan menambahkan *constructor* baru dan mengisi parameter yang dibutuhkan sesuai keinginan kita. Tetapi hal ini sebenarnya *redundant* /berulang. Bayangkan apabila teman-teman setiap menciptakan sebuah *object* yang parameternya berbeda-beda dari *constructor* yang sudah ada. Kurang fleksible. Dalam kasus ini, **lombok** memberikan sebuah jawaban, yaitu `**@Builder**` . Kita sudah punya *class* `**Tour**` dengan anotasi `**@AllArgsConstructor**` . Jika kita ingin membuat sebuah *object* *tour* , kita akan buat :

```
**Tour tour = new Tour(1, "title", "desc", 500, "3 days", "keywords", Difficulty.HARD, Region.CENTRAL_COAST);**
```

Dengan menggunakan `**@Builder**` pada *class* `**Tour**` , kita bisa lebih bebas dalam menciptakan sebuah *object* .

Setelah melihat banyak perbandingan tadi, apakah teman-teman masih tidak ingin menggunakan **lombok** ? (bukan promosi 😆). Silahkan teman-teman eksplorasi tentang lombok dan coba terapkan pada *class* data *model* kita kali ini.

*See you in the next part* 🔥

## Note:

Teman — teman boleh cek dokumentasi lombok [di sini](https://projectlombok.org/) .