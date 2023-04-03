# Bagaimana Cara Menentukan Pilihan Bahasa Pemrograman yang Akan Dipelajari Selanjutnya?

> 原文：<https://medium.easyread.co/bagaimana-cara-menentukan-pilihan-bahasa-pemrograman-yang-akan-dipelajari-selanjutnya-25fdd04b8391?source=collection_archive---------0----------------------->

## Bahasa pemrograman apa yang akan saya pelajari, setelah yang telah saya mengerti saat ini?

![](img/d510a98e85feb87ee533342ed4f5a974.png)

Photo by [Pankaj Patel](https://unsplash.com/@pankajpatel?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Cerita berikut hanyalah sekilas *intermezzo* dibalik kesibukan saya di kantor yang semakin hari membuat penat. Demi mengurangi tekanan pikiran, saya tumpahkan semua kedalam tulisan agar sedikit lega :D

*Nah,* di tulisan berikut, saya sedikit membahas tentang opini dan teorika, hal yang tidak terlalu praktikal, yakni tentang bagaimana menentukan bahasa pemrograman yang akan kita pelajari selanjutnya setelah yang telah kita ketahui saat ini.

Untuk menentukan bahasa pemrograman yang ingin dipelajari/dikuasai, saya akan fokus pada 2 level, yaitu level professional dan level tahapan ke professional. Jika pembaca adalah pelaku *startup* digital, tulisan ini mungkin kurang cocok untuk menjadi bahan pertimbangan kepada produk anda.

## **Mahasiswa atau Undergrade**

Untuk seorang mahasiswa, hal yang perlu diketahui terlebih dahulu tentunya konsep programming yang dipelajari ketika kuliah. Fokus pada penyelesaian masalah, *algorithm dan solving problem* (kalau kata orang luar :D). Selanjutnya adalah pelajari *engineering practices* yang awam dilakukan pada dewasa ini, seperti Git, REST, CI/CD, *Microservices* (kebetulan topik ini juga sedang *booming* saat ini) — *khusus backend ya hehe, karena saya backend saya menulis hal yang perlu diketahui mahasiswa sejak dini jika hendak ke dunia Backend.*

Untuk bahasa pemrograman, kamu cukup pilih satu aja yang kamu suka, baik *intepreted* , ataupun *compiled* , tidak masalah. Selanjutnya kuasai semua hal terkait bahasa tersebut, *how they works behind the scenes* , kalau using *Garbage collector* , pelajari bagaimana *flow* -nya, kalau pake *async* , pelajari hal tersebut hingga cukup paham.

Sehingga, saat kamu *interview* ke perusahaan, semua ini akan berguna. Mereka melihat keseriusan dan dedikasi yang ada pada diri anda karena anda meluangkan waktu untuk belajar banyak tentang hal tersebut.

## **Professional atau Freelance (Level Bekerja)**

![](img/89c9c061ef209cb6de153d8d958b0185.png)

Photo by [bruce mars](https://unsplash.com/@brucemars?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

Untuk level bekerja, menentukan bahasa pemrograman diawali dengan pertanyaan “What”. *What’s the problem* ? Apa masalahnya?

Sebelum menentukan belajar bahasa pemrograman baru, tentukan dulu apa masalah yang sebenarnya anda hadapi, sehingga memaksa anda untuk pindah bahasa pemrograman. Apa kelemahan bahasa pemrograman yang anda pakai sekarang sehingga anda harus melirik bahasa baru? Terkadang rumput tetangga memang lebih hijau, tetapi apakah hanya karena *fresh* dan hijau, maka anda harus melepaskan yang anda pegang saat ini?

Untuk lebih jelas, mungkin saya akan jelaskan dalam bentuk contoh. Saya akan angkat dari masalah yang ada pada **Kurio** (tempat saya bekerja ketika menulis ini).

Di Kurio, sebelum saya bergabung, kami memiliki sebuah API Gateway yang ditulis dari bahasa PHP dengan menggunakan *framework* Laravel. API Gateway ini di akses langsung oleh seluruh user kami, hingga mencapai 8000 RPS (masih cukup kecil dibanding *ecommerce* ). Namun, *cost* untuk infra struktur kami lumayan gendut, bayar server cloud di AWS cukup menepok jidat untuk segala hal, *just AWS being AWS* , semua hal pasti kena *charge* :v

Ini adalah masalah. *What’s the problem? Cost is too expensive!* Alih-alih mencari jalan keluar untuk menghemat pengeluaran biaya server, kami menemukan alternatif, yakni Golang yang katanya cepat dan hemat.

Kebetulan Golang yang saat itu hingga sekarang juga lagi masa-masa popularnya, ya namanya *up-to-date engineers* , pengen dong nyobain hal baru. *We must learn everyday and improve our skills!!* (Begitulah kata orang-orang bijak :D)

Kami pun sepakat menggunakan Golang sebagai pengganti PHP yang kami gunakan sudah lebih dari 3 tahun. *Let’s re-build the entire systems babyyy!!!!!* :D

6 bulan pengerjaan, akhirnya kami berhasil *migrate* dari PHP ke Golang, semua sistem. Hasilnya cukup ketara dan memuaskan. Dengan specs yang sama, dulu ketika menggunakan PHP saat *high peak request* , semisal kami menggunakan 8 fisik server, tetapi ketika pindah ke Golang, kami hanya butuh 4 server. Ini adalah pencapaian.

Namun, dibalik pecapaian tersebut, kami menemukan beberapa kasus, khususnya ketika me-rebuild API Gateway kami dari PHP ke Golang. Ada hal yang membuat kami sedikit terbatas ketika menggunakan Golang.

Yaitu, golang terlalu statis, *Golang doesn’t has the generic types* , dan ketika menggunakannya menjadi API Gateway, kami melakukan banyak *redundancy code* yang seharusnya tidak ada di API Gateway.

*What’s the problem? Golang too static!! Can’t used as an API Gateway!* Kepikiran untuk mencari *dynamic programming language but fast* , kami ada 2 pilihan untuk kami pelajari, Node JS atau *functional programming language* .

Dengan *traffic* yang cukup besar, dan berlandas dari pengaruh eksternal, *“No body use Node JS in high concurrent application”* sebut saja Traveloka denger-denger dari teman dalam, pakai Java, sebut juga Blibli it’s about Java, Tiket juga menggunakan Java (dari orang dalam :D), Tokopedia, menggunakan Golang (dari orang dalam :D), Grab menggunakan Golang (dari orang dalam :D) dan Gojek juga menggunakan JVM dan Golang (dari orang dalam juga :D), Bukalapak? (saya tidak ada kenalan orang dalam yang backend :D hehe, jika ada yang baca tulisan saya ini, salam kenal guys :D).

Akhirnya karena pertimbangan hal tersebut, Node JS bukan menjadi pilihan kami untuk membangun API Gateway. Akhirnya kami membangun API Gateway kami sendiri menggunakan Clojure (meski saya tidak terlalu paham haha, saya tidak tergabung dalam tim ini, ketika itu saya ada misi lebih mulia :D, jadi tidak sempat ikut belajar dan bekerja menggunakan Clojure :D), soal performance, bukan rahasia juga, *Clojure running on JVM,* juga clojure adalah *functional programming language* dan dinamis, sangat cocok untuk API gateway yang sekedar *routing* dan *passtrough,* *so problem solved* .

Problem utama yang muncul ketika menggunakan Clojure hanyalah *readibility dan learning curve* nya cukup sulit buat saya, saya tidak tahu dengan teman-teman engineer lainnya. Saya masih cukup sulit memahami cara Clojure bekerja.

Dari masalah diatas, dengan ini bisa disimpulkan, untuk menentukan bahasa pemrograman yang akan kita pelajari selanjutnya adalah tergantung dari masalah yang hendak kita pecahkan. Ini juga berlaku untuk semua teknologi *stack* yang ada, baik *database* , *message-broker* , *framework* , dsb. Dimulai dari masalah yang anda alami, lalu pelajari hal yang mungkin bisa memecahkan masalah tersebut.

Selanjutnya adalah, faktor eksternal. Bukan saatnya menjadi apatis, orang-orang diluar sana sudah lebih dulu mengalami masalah yang sama, sehingga ketika menentukan bahasa pemrograman baru selanjutnya, perlu dilihat juga dari luar. Sama halnya ketika kami tidak memilih Node JS untuk menjadi main API Gateway kami, perusahaan besar yang saya sebutkan diatas, tidak menggunakan Node JS untuk menangani service yang *concurrency* -nya sangat besar, dan memilih yang lebih terjamin seperti JVM.

Jadi, demikian tulisan saya. Semoga dapat membantu anda dalam menentukan bahasa yang ingin dipelajari. Semua isi dari tulisan berdasarkan pengalaman dan pendapat saya :)