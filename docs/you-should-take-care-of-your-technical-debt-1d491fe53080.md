# You Should Take Care of Your Technical Debt

> 原文：<https://medium.easyread.co/you-should-take-care-of-your-technical-debt-1d491fe53080?source=collection_archive---------8----------------------->

## Bahaya technical debt jika dibiarkan berlarut-larut

![](img/68b3792afd8029deacd041363c2b226e.png)

Sticky Notes

Jacky Mao adalah seorang *software developer* yang baru saja diterima bekerja di sebuah perusahaan. Jacky diberi tanggung jawab untuk menangani sebuah aplikasi web. Tugas pertamanya adalah menambah field di sebuah *form* berikut *business logic* disisi *back-end* . Awalnya dia diberi waktu 5 hari untuk menyelesaikannya. Namun kenyataannya butuh 3 minggu hanya untuk menambah satu *field* . Sebuah kisah yang cukup membuat tercengang. Tetapi pada faktanya kasus di atas sering ditemukan di industri *software development* . Kejadian di atas adalah salah satu contoh sederhana untuk kasus yang lebih kompleks. Jacky sendiri adalah salah satu dari sekian banyak *developer* yang berjuang susah payah menghadapi *software* yang penuh dengan ***technical debt*** .

Dari kisah di atas dapat dilihat bahwa Jacky menghabiskan sebagian besar waktunya berkutat memahami struktur kode ketimbang menambah fitur. Kompleksitas dari tugas yang diberikan yaitu menambah satu *field* , menjadi lebih besar dari yang seharusnya. Ini terjadi karena struktur kode dari aplikasi yang dikerjakan tidak teratur, tidak konsisten, dan tidak deskriptif. Jangan tanya apakah dokumentasi ada? Sudah pasti tidak. Struktur kode menjadi sedemikian kompleks sehingga terdapat 8 layar abstraksi mulai dari *user* menambah data sampai disimpan ke *database* . Sungguh mengagumkan.

Beberapa faktor yang dapat menyebabkan kasus seperti di atas terjadi, yaitu:

*   Kemampuan teknis *developer* yang rendah
*   Kurangnya kepedulian ( ***if it ain’t broken why fix it*** )
*   Mengambil jalan pintas untuk menyelesaikan sebuah tugas ketimbang menganalisis dan memecah permasalahan menjadi beberapa bagian kecil
*   Mengimplementasikan ulang solusi yang sudah ada namun hasilnya lebih buruk (contoh: mengimplementasikan XML *parser* sendiri)
*   *Deadline* yang tidak masuk akal

Berbicara mengenai *deadline* . *Developer* mendapat tekanan untuk menyelesaikan tugas. Waktu yang diberikan sebenarnya tidak cukup untuk menghasilkan solusi yang optimal untuk jangka panjang. Namun *sales* terlanjur menjanjikan fitur yang diminta selesai kepada klien dalam kurun waktu tersebut. Jalan pintas pun ditempuh dengan menulis kode yang tidak optimal tapi bekerja sesuai ekspektasi klien. Jika ini berlanjut maka aplikasi yang sedang dikerjakan akan dipenuhi dengan kode yang buruk. Kode yang bahkan penulisnya sendiri pun akan bingung membacanya 3 bulan ke depan. Kode yang baik seharusnya dirancang dengan baik terlebih dahulu sebelum baris pertama ditulis.

*Developer* harus jeli menganalisis berapa lama waktu yang diperlukan untuk menulis kode. Diskusikan dengan atasan. Diskusi yang baik menghasilkan kesimpulan yang baik. Jadikan [**Hofstadter’s Law**](https://en.wikipedia.org/wiki/Hofstadter%27s_law) sebagai pedoman.

> *It always takes longer than you expect, even when you take into account Hofstadter’s Law.*
> 
> *Douglas Hofstadter*

Hukum ini banyak dipakai untuk menjustifikasi perkiraan berapa lama sebuah tugas selesai dikerjakan, tidak terlepas dunia *software development* . Terkadang sebagai *developer* kita tidak mampu menentukan estimasi waktu yang dibutuhkan untuk menyelesaikan sebuah tugas. Estimasi waktu sebisa mungkin harus fleksibel namun tetap dalam pengawasan supervisor sehingga memberi ruang untuk kemungkinan yang tidak terduga. Di sini saya mau menambahkan pentingnya bekerja di sebuah tempat yang memberi ruang untuk *feedback* .

Jalan pintas tidak selalu salah. Dengan mengambil jalan pintas memungkinkan perusahaan untuk menjual sebuah *software* atau fitur sesegera mungkin. Dengan memangkas waktu menjadikan produk lebih cepat terjun ke pasar dan lebih awal mendapat *feedback* . Namun ada harga yang harus dibayar. Jalan pintas diambil dengan mengurangi waktu yang seharusnya diperlukan untuk menghasilkan solusi. Katakanlah waktu yang optimal untuk menambah sebuah fitur adalah 3 minggu, namun dipangkas menjadi seminggu untuk mengejar *deadline* . Karena waktu berkurang maka *developer* terpaksa menulis kode yang jelek. Kode yang naik ke *production* seharusnya dalam bentuk yang optimal namun karena waktu tidak cukup maka yang terjadi sebaliknya. Si *developer* “berhutang” dan nantinya hutang tersebut harus dibayar dengan memperbaiki strukur kodenya menjadi lebih baik. Dari sini muncullah istilah ***technical debt*** (hutang teknis).

*Technical debt* menjadi masalah jika tidak dikelola dengan benar. Seiring waktu berjalan maka hutang demi hutang akan menumpuk dan menuntut untuk “dibayar” karena akan berimbas pada *future development* . Hutang teknis yang semakin banyak akan memperlambat *progress* ke depannya sampai hutang tersebut dibayar. Hutang dibayar tidak hanya dengan memperbaiki kode tapi juga perlu menambah test dan dokumentasi. Jika hutang teknis terlalu banyak dan sulit untuk dibayar maka terdapat kemungkinan si *develope* r menjadi tidak lagi betah bekerja dan akhirnya pindah perusahaan. Jacky tentu tidak ingin menangani aplikasi *web* yang butuh 3 minggu hanya untuk menambah sebuah *field* .

Namun tidak semua perusahaan peduli dengan masalah ini karena alasan sederhana “ *as long as it works* ”. Bagi sebagian besar perusahaan pekerjaan memperbaiki kode ( **refactoring** ) dirasa bukanlah sesuatu yang penting. *Refactor* dilihat tidak menghasilkan keuntungan. Mengapa kode perlu diperbaiki jika selama ini sudah berjalan dengan baik dan mencetak profit. Dari sudut pandang perusahaan, atau lebih tepatnya bisnis, teknologi hanyalah salah satu alat untuk mencapai tujuan, yaitu menghasilkan keuntungan. Jika PT. ABC berhasil menjual sebuah *software* yang dipakai di 100 negara dan sudah mencetak untung besar, maka *technical debt* bukanlah sesuatu yang penting. Karena bagi perusahaan tolak ukur keberhasilan sebuah *software* adalah berapa banyak fitur yang bisa dijual.

Bagi manajemen, kode yang lebih optimal dilihat tidak memberi efek yang bisa dirasakan langsung oleh *customer* . Hal ini benar karena yang diuntungkan sebenarnya adalah *developer* . Sehingga wajar bagi mereka untuk sulit memahami pentingnya pengelolaan *technical debt* . Walaupun si manajer punya latar belakang teknis tetap saja sulit baginya untuk memahami jika tidak terlibat langsung dengan kodenya. Mustahil untuk menjustifikasi pentingnya *refactor* . Meskipun begitu *developer* tidak boleh melangkahi manajer. Karena mereka lebih tahu strategi perusahaan, alokasi bujet, dan pengelolaan risiko. Ingat bahwa tugas Anda sebagai *developer* adalah menambah *value* lebih dari menulis kode. Pekerjaan Anda dihargai jika mendatangkan keuntungan bagi perusahaan.

Jika bagi Anda *technical debt* sama pentingnya dengan menambah *value* , **selamat!!!** Anda satu langkah lagi menjadi *good developer* . Silahkan melanjutkan bacaan di bawah.

Hutang teknis yang terlalu banyak, sama halnya hutang finansial, jika tidak sanggup dibayar akan menyebabkan kebangkrutan. Di dunia *software development* bangkrut bisa diartikan sebagai menulis ulang semua kode dari awal. Dari nol. Namun menulis ulang kode sulit untuk dilakukan, berisiko tinggi, dan harusnya pilihan terakhir. Mencegah lebih baik daripada mengobati. Jika mengambil jalan pintas sulit untuk dihindari maka mau tidak mau kita menambah hutang teknis. Jika demikian maka sedari awal *developer* harus peduli dengan hutang teknisnya. Mulailah mencatat setiap jalan pintas yang diambil. Catat setiap baris kode yang tidak optimal. Pergunakan buku catatan, *sticky notes* , *spreadsheets* , Github Issues, atau media lainnya sebagai dokumentasi. Usahakan semuanya tercatat. Bangun kebiasaan baik. Mulailah mencatat poin-poin di bawah ini:

*   Kode yang dirasa tidak perlu
*   Kode yang sulit dibaca
*   Kode yang tidak optimal
*   Kode yang dirasa memperlambat progres *development* ke depannya
*   Jika dibutukan waktu lebih lama dari yang seharusnya untuk mengerjakan sesuatu berarti kemungkinan ada kode yang jelek
*   Kode yang tidak memilki tests (opsional)
*   Kode yang tidak memiliki dokumentasi (opsional)

Belajar untuk jujur terhadap diri sendiri dan catat apa adanya. Tidak perlu malu karena *you are not your code* . Seperti yang sudah dijelaskan di atas, *technical debt* juga merupakan investasi dengan catatan dikelola dengan baik. Buatlah jadwal, misalnya setiap dua minggu, untuk mendiskusikan langkah yang perlu ditempuh dengan *technical debt* yang ada. Analisis waktu yang diperlukan untuk *refactoring* . Tentu saja waktu Anda menjadi terbagi untuk menambah fitur baru dan *refactoring* . Tapi jika dengan *refactor* yang tadinya butuh 3 minggu untuk menambah sebuah *field* , berkurang menjadi 3 hari, 3 jam, atau bahkan 3 menit, maka *refactoring* menjadi sangat menguntungkan. Diskusikan dengan atasan kode mana yang paling memerlukan *refactor* . Susun daftar prioritas.

Meskipun sebuah kode terlihat tidak optimal namun dirasa tidak membebani progres *development* ke depannya, tidak ada salahnya untuk tetap dicatat. Anda tetap bisa menghapusnya dari daftar di lain hari setelah melalui diskusi. Perlu diingat daftar tersebut bukanlah *silver bullet* . Anda tetap harus mengkaji ulang apakah sebuah kode termasuk *technical debt* atau tidak. Di sini diperlukan kejelian. Ini bukan soal Anda suka atau tidak dengan kodenya tapi lebih kepada imbasnya di masa depan. Jika Anda atau anggota tim Anda ingin menambah *technical debt* baru, diskusikan terlebih dahulu apakah penambahan tersebut sepadan dengan keuntungan yang diperoleh.

Jika *technical debt* terlanjur menjadi terlalu banyak, solusi yang tepat untuk menanganinya adalah ***Clean as you go, Refactor as you go*** . Prinsip yang juga banyak diterapkan di sektor industri lain. Artinya aktivitas menambah fitur baru berjalan beriringan dengan *refactoring* , tidak terpisah. Setiap kali Anda menambah atau mengurangi baris kode, saat itu juga Anda melakukan *refactoring* terhadap baris kode yang berhubungan. Pakai waktu ekstra untuk menambah tests. ***Small improvements in small iterations*** . Sedikit demi sedikit lama-lama menjadi bukit. Lakukan setiap harinya dan setelah beberapa bulan kebiasaan ini akan membawa dampak positif bagi pekerjaan Anda. Karena dengan *refactor* Anda menolong diri Anda sendiri di masa depan dengan mengurangi waktu yang diperlukan untuk menulis kode. Waktu yang berkurang memungkinkan Anda untuk menambah lebih banyak fitur dan pada akhirnya membuat atasan senang. Promosi jabatan pun datang. Waktu juga termasuk sumber daya yang harus dipakai sebaik mungkin.

Jadi Anda sudah mulai *refactoring* ? Bagus!

***Refactor*** adalah proses mengubah struktur kode sedemikian rupa tanpa mengubah *behavior* (perilakunya). Dengan tujuan meningkatkan kualitas kodenya. Misalkan kita punya tabel *product* dan fungsi *update()* untuk memperbarui data setiap produk. Fungsi tersebut terdiri dari 20 baris kode yang belum optimal. Jika kodenya bisa diperbaiki dan barisnya berkurang, katakanlah menjadi 10, inilah contoh *refactoring* yang berhasil. Hasil akhir yang diperoleh adalah fungsi yang lebih optimal tanpa merubah apa yang dikerjakannya. Kita hanya menyusun ulang struktur kodenya. *Refactoring* akan lebih mudah dilakukan dengan adanya tests. *Tests* adalah bagian yang sangat berguna dari setiap *software* . *Unit tests* , *integration tests* , *UI tests* , *architecture tests* , *performance tests* , *behaviour-driven tests* , *specific* *framework integration tests* , dan lainnya. Mungkin Anda sudah biasa menulis tests. Dengan menjalankan tests setiap kali mengubah kode adalah cara terbaik untuk memastikan segala sesuatunya tetap berjalan sebagaimana harusnya. Mencegah terjadinya regresi. Tapi pilihan tetap berada di tangan Anda. Jika menulis tests dirasa membuang waktu maka semuanya kembali ke Anda sendiri.

*Jika Anda tetap merasa semua poin di atas tidak terlalu penting, pertimbangkan* [*kutipan*](https://news.codecademy.com/bjarne-stroustrup-interview/) *dari Bjarne Stroustrup, pencipta C++ ini.*

> *Our civilization depends on good software.*
> 
> *Bjarne Stroustrup*