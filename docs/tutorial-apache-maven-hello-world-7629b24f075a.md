# Tutorial : Apache Maven “Hello World”

> 原文：<https://medium.easyread.co/tutorial-apache-maven-hello-world-7629b24f075a?source=collection_archive---------7----------------------->

## Bagaimana menggunakan maven?

![](img/26954db8c2c5a5f38c5fea5767e3ca9a.png)

Haloo, pada tulisan kali ini, saya akan membahas mengenai *project* *management* dan *comprehension tool* dengan menggunakan maven. Salah satu manfaat dari penggunaan maven yaitu dapat memberikan manfaat untuk *build process* dengan menerapkan konvensi dan praktik standar untuk mempercepat siklus *development* . Sebagai pembelajaran awal, dibawah ini ada tutorial membuat hello world dengan pemograman java + maven.

# Maven’s Objectives

Tujuan utama dari maven adalah untuk memahami tahap *development* dalam waktu yang singkat. Untuk mencapai tujuan ini, ada beberapa bidang yang perlu diperhatikan pada maven, yaitu

*   Membuat proses *build* menjadi lebih mudah.
*   Menyediakan sistem *build* yang seragam.
*   Memberikan informasi proyek yang berkualitas.
*   Memberikan pedoman untuk pengembangan praktik terbaik.
*   Mengizinkan migrasi transparan ke fitur baru.

# Working With Maven in IntelliJ IDEA

Untuk mempermudah dalam memahami penggunaan maven, maka dibawah ini akan ada contoh penggunaan maven dengan menggunakan IntelliJ IDEA.

![](img/f729bb6e345efa4dde4c1e9a51521cc2.png)

*   **Klik Create New Project**

![](img/e679c7eb4a50bbaefb60765cb2e3f17a.png)

*   **Pilih Project SDK. Untuk contoh ini akan menggunakan maven-archetype-quickstart**
*   **Klik Next**

![](img/544768814c5f891cac6355052c7e6d33.png)

GroupId adalah namespace atau nama package dari aplikasi. ArtifactId adalah nama direktori dari aplikasi. Version adalah versi dari aplikasi

*   **Isi GroupId , ArtifactId dan Version**

![](img/9fccf9f47107e7290de926e8303aec04.png)

*   **Klik next**

![](img/7dc7de266c8e29072ef072fcacc2fc11.png)

*   **Isi nama project dan directory project**
*   **Klik finish**

![](img/928c927089f7acf13575a4ae44c753f1.png)

Setelah semua langkah dilakukan maka akan ada struktur folder sebagai berikut

![](img/9af8d5eb2c90bb2394c3ebb27d7aa0ca.png)

Run perintah berikut di dalam folder introduction-to-medium

```
$ mvn exec:java -Dexec.mainClass=”id.medium.App”
```

Maka akan muncul hello world sebagai berikut

![](img/4e6efecd52d93249fb4bcc29fb49fedd.png)

Semoga tutorial ini dapat membantu untuk belajar maven. ***Good Luck*** !!

# **Referensi**

[](https://maven.apache.org/) [## Maven - Welcome to Apache Maven

### Apache Maven is a software project management and comprehension tool. Based on the concept of a project object model…

maven.apache.org](https://maven.apache.org/)