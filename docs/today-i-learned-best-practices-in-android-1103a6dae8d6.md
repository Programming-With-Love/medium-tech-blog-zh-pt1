# Today I Learned : Best Practices in Android

> 原文：<https://medium.easyread.co/today-i-learned-best-practices-in-android-1103a6dae8d6?source=collection_archive---------1----------------------->

## Part of [Today I Learned Series](https://medium.com/easyread/today-i-learned/home) in Easyread

![](img/20759d4d6177967a3472952abb281ea2.png)

Hari ini saya mengikuti sebuah *event* yang diselenggarakan oleh GDP Venture, yaitu **GDP Venture Tech Day** . Pada *event* yang bertemakan *Tech-Day* ini, tentu saja terdapat berbagai topik *tech-talk* yang diberikan oleh orang-orang berpengalaman. Misalnya, tentang ***Technology: Challenges* & *Opportunities* , *Introduction to DevOps* , *Best Practices in Android*** , dan lain sebagainya. Pada kesempatan kali ini, saya memilih 2 topik, yaitu ***Best Practices in Android*** dan ***Implementing Psychology to UI/UX Design*** *.*

![](img/e9cccb51e8de1f4b467e9022b7c1fcf0.png)

*Nah* , setelah mengikuti kedua *tech talk* diatas, sekarang saya ingin membagikan beberapa hal tersebut melalui tulisan ini tentang apa yang saya pelajari pada hari ini.

# Best Practices in Android by — Sumeet Singh

## Architecture

Berbicara mengenai arsitektur, dalam mengembangkan aplikasi android dikenal beberapa arsitektur yang sering dipakai, seperti **MVC** , **MVP** , dan **MVVM** . Tujuan menggunakan arsitektur ialah ***separation of* *concern*** atau memisahkan *class* berdasarkan tujuan sehingga lebih teratur, *maintainable* dan *testable* .

Membuat aplikasi android dengan arsitektur tertentu berarti membuat aplikasi yang memiliki ***Base* & *Wrapper Class*** . ***Base class*** adalah sebuah *class* yang menjadi dasar aplikasi, dibuat dengan mengidentifikasi proses yang terjadi dan jika terdapat kesaamaan proses letakkan pada ***base class*** .

Pada presentasi yang disampaikan, ***Sumeet Singh*** memberikan beberapa *tech stack* yang biasa digunakan untuk mengembangkan aplikasi Android, yaitu.
- Network: Retrofit dan Okhttp ( *Interceptor* dan *Authenticator* )
- Image: Glide.
- Dependency dan Reactive Programming: Dagger, RxJava dan EventBus.
- Persistence: Sqlite dan Room.
- Logging: Timber, Logback Android dan Crashlytics.

## Delivery

Berbica mengenai aplikasi yang akan di- *deliver* , berarti membahas mengenai aplikasi yang akan dijual nantinya kepada *user* . Beberapa hal yang perlu diperhatikan selama proses pengerjaan ialah.
**- *Reusable Component*** . Saat mengembangkan sebuah fitur, pikirkan fitur ini kedepannya akan seperti apa, proses dan cara kerja yang paling baik apa, jangan langsung dituangkan ke dalam kode. Jadikan sebuah komponen yang *reusable* akan lebih baik, sehingga dapat dipakai kembali.
**- *Refactoring*** *.* Ambil waktu untuk melakukan *refactoring* untuk kode yang telah kita buat. Hal ini diperlukan untuk memperbaiki kodingan yang ada agar lebih baik.
**- *API Versioning and Backward Compability*** . Berbeda dengan aplikasi web, setiap perubahan yang terjadi di **API** tidak dapat secara langsung di adaptasi oleh android, jadi lakukan *versioning* .
**- *Application Config*** . Lakukan *quick testing* , dan konfigurasikan *resource* yang dibutuhkan *.* ***- Stagged Rollout-Google Play*** . Lakukan [*stagged-roll out*](https://support.google.com/googleplay/android-developer/answer/6346149?hl=en) , setiap ingin melakukan rilis aplikasi. Hal ini akan membantu kita dalam men- *deliver* aplikasi yang baik ke *user* .

## Feedback/Stability

Saat mem- *publish* aplikasi pada *Google Play Store* , akan ada halaman yang menampilkan [*feedback*](https://blog.pairworking.com/what-are-the-common-challenges-faced-by-mobile-developers-2f2ff81ad29b) pada aplikasi kita. Perhatikan setiap masukan dari *user* untuk mendapatkan *review* aplikasi. Selain itu akan membantu kita menganalisis kebutuhan *user* . Gunakan ***In App Feedback*** . untuk mengisolasi *feedback* yang buruk.

## Personalization

Kembangkan aplikasi kita dengan personalisasi agar setiap *user* mendapatkan pengalaman tersendiri dengan aplikasi kita.

## **Security**

Untuk masalah *security* kita dapat melakukan beberapa hal, yaitu.
**-** **Thread Modelling** ( [OWASP Mobile Top 10](https://www.owasp.org/index.php/OWASP_Mobile_Security_Project) )
**- Dynamic and Static Analytics.
- Penestration Testing
- Protecting Data At Rest
- Root Device Check
- Storing Key:** *Clide side code* ( *proguard* ), *provide data in motion* ***-* HTTPs** : *Certificate pinning* ***-* Backend Services**

## Analytics

Untuk *analytics* hal ini diperlukan untuk menganalisis data yang ada tentang penggunaan aplikasi dan menyimpulkan aplikasi yang kita miliki sudah seperti apa. Biasa salah satu data yang penting misalnya ***Retention Rate Day-1, Day-7*** , dan ***Day-30* .** Setelah itu gunakan data untuk *Bussiness Intelligence* untuk mengetahui apa yang baik dari aplikasi. Kelompokkan data, lihat *behaviour* dari aplikasi, *gender* pemakai aplikasi, dsb. untuk membangun fitur yang baik.

# Q&A with — Sumeet Singh

*Fyi* , pertanyaannya sedikit saya ubah dengan tujuan yang sama, karena sedikit ketinggalan untuk mencatat pertanyaannya. Jawabannya juga saya sampaikan dalam Bahasa.

## 1\. How you manage your team. Develop they and have a same idea and vision to deliver good application?

Untuk setiap anggota tim, lebih berfokus kepada pemahaman mengenai algoritma, pola pikir dan *fundamental* . Tidak terfokus kepada *framework* apa yang diketahui. Mengetahui *framework* lain tentu saja merupakan nilai *positif.* Biasaya 2 minggu untuk pemograman dasar lalu selanjutnya mengenai *framework* . Selain itu ada evaluasi teknologi.

## **2\. There is a lot of Architecture. How to choose them in small team?**

Semua arsitektur memiliki pro dan kontra. Tidak ada yang salah. Yang perlu dilakukan ialah mengetahui pro dan kontra dari arsitektur dan kembangkan sesuai kebutuhan. Mengetahui *requirement* yang akan dibangun dan tentukan pilihan.

## **3\. Which better for mobile technology? iOS or Android?**

Kita membangun aplikasi di iOS dan Android. Semua berdasarkan kebutuhan pengguna.

Sekian mengenai apa yang saya pelajari hari ini. Mungkin ada beberapa yang saya tambahkan sesuai pemahaman saya, atau ada beberapa hal yang terlewat. Jika dari teman-teman ada yang ingin menambahkan, dapat memberikan masukan pada komentar. *Happy Learning* 😄

*如果您有任何问题或抱怨，请随时联系我这些社交网络:* [*twitter*](https://twitter.com/eyseminarti) *，*[*LinkedIn*](http://linkedin.com/in/eminarti-sianturi-08a369102)*，或*[*email*](mailto:eminartiys@gmail.com)*。能回答的我尽量回答，不然我们一起学:)*

感谢——苏米特·辛格。