# Christmas Tale of Sofware Engineer: Project Kube-Xmas

> 原文：<https://medium.easyread.co/christmas-tale-of-sofware-engineer-project-kube-xmas-9167ebca70d2?source=collection_archive---------6----------------------->

## **Part 1: Sample Project and Plan**

![](img/983b2ad8747cde07d69620ec2b8353da.png)

Photo by [Tom Rickhuss](https://unsplash.com/@tomrickhuss?utm_source=medium&utm_medium=referral) on [Unsplash](https://unsplash.com?utm_source=medium&utm_medium=referral)

*So...* Hari ini adalah waktu-waktu yang memberikan sukacita khususnya buat teman-teman umat nasrani. Jika tahun lalu, di malam Natal saya membagikan cerita kegabutan saya di tulisan: [***Christmas Tale of Software Engineer***](https://hackernoon.com/a-christmas-tale-of-software-engineer-8a8ffa21b0c0) . Maka di tahun ini juga, ketika saya masih tidak dapat kembali ke pangkuan ibu negara di kampung, saya memutuskan untuk mengisi hari saya dengan kegiatan yang cukup terencana.

Sebenarnya saya sudah merencanakan tulisan ini dari tanggal 23 Desember 2018 kemarin, sehingga saya sudah mempersiapkan beberapa *draft* pada hari itu. Jadi pada malam ini saya hanya menambah dan memoles-moles beberapa bagian saja, layaknya menambahkan beberapa kado di bawah pohon Natal sebagai pelengkap.

> Plannya adalah saya akan membuat sebuah series mengenai ***Kubernetes*** dan Docker.

**Saya peringatkan teman-sekalian, jika semua artikel ini menurut anda useless dan terlalu mudah, saya mohon maaf. Karena ini hanyalah keisengan pribadi saya pada Natal tahun ini. Saya pikir, daripada melakukan hal gabut yang tidak jelas, lebih baik bereksperimen kecil dengan hal yang berguna :D*

**Semua hasil project explorasi ini dapat di-akses direpo github saya:* [*https://github.com/bxcodec/tweetor*](https://github.com/bxcodec/tweetor)

**— Part 1: Sample Project and Plan [artikel ini]**
Di *part* ini, saya akan menjelaskan apa yang akan saya kerjakan sebagai acuan series yang akan saya tulis ini.

**—** [**Part 2: Belajar Membuat *Kubernetes Cluster* di Google Cloud**](https://medium.com/easyread/membuat-kubernetes-cluster-di-google-cloud-ae14bee317ba)
Kenapa *Google Cloud* ? Tidak ada alasan spesial. Di Kurio, perusahaan tempat saya bekerja ketika menuliskan ini, menggunakan *Google Cloud* untuk *Kubernetes* provider kami. Sehingga, harapan saya nantinya series ini bisa menjadi bahan bacaan untuk *engineer* baru di Kurio.

**—** [**Part 3: Setting NGINX Ingress Controller di Kubernetes Google Cloud**](https://medium.com/easyread/setting-nginx-ingress-controller-di-kubernetes-google-cloud-10f2c9c0be16)
Kenapa Nginx? Lagi, tidak ada alasan spesial, kami di Kurio juga menggunakan Nginx sebagai ingress controller kami di Kubernetes.

**—** [**Part 4: Membuat *Mock Server* dengan OpenAPI 3 di Kubernetes**](https://medium.com/easyread/membuat-mock-server-dengan-openapi-3-di-kubernetes-b9963ed2ac40)

**—** [**Part 5: Men-dockerize Aplikasi Golang**](https://medium.com/easyread/men-dockerize-aplikasi-golang-9c32959c657e)
Kenapa *Golang* ? Lagi, tidak alasan spesial. *Main programming language* yang sering saya dan Kurio gunakan adalah Golang. Sehingga pada project Kube-Xmas ini, saya menggunakan Golang sebagai bahasa utama pemrograman saya.

**—** [**Part 6: Membuat Development Environment Aplikasi Golang dengan Docker Compose**](https://medium.com/easyread/membuat-development-environtment-aplikasi-golang-dengan-docker-compose-4e96542c19ea)

**—** [**Part 7: Membuat *Makefile* untuk aplikasi Golang**](https://medium.com/easyread/membuat-makefile-untuk-aplikasi-golang-5c2d19122b13)

**—** [**Final Chapter: Men- *deploy* Aplikasi Golang ke *Kubernetes***](https://medium.com/easyread/men-deploy-aplikasi-golang-ke-kubernetes-6c91c67f35b5)

# Budget dan Pre-requisite

Karena saya menggunakan Google Cloud, serta domain yang nyata, (saya beli satu domain murah untuk keperluan tulisan ini :D), jika teman-teman pembaca hendak mengikuti, saya akan buat list yang perlu disiapkan sebelum melangkah jauh :D. Kalau tidak punya, mungkin sambil dibaca saja sudah cukup memberikan pemahaman bagaimana prosessnya kok :)

## Wajib

*   Kartu Kredit/Debit untuk kebutuhan verifikasi billing di Google cloud. Verifikasinya cukup ribet, saya sarankan jangan memakai virtual credit/debit card. Gunakan kartu fisik untuk kebutuhan verifikasi. Saya menggunakan kartu debit Jenius, sudah oke kok.
*   KTP (kartu identitias), hanya untuk kebutuhan verifikasi billing di Google cloud
*   Domain aktif dan belum dipakai. Kamu bisa beli di Google domain, atau Godaddy, Namecheap dsb.

## Budget

Ketika menulis artikel ini serta meng-implementasikanya, saya hanya habis Rp 17.000 ( US $1,17 — current exchange-rate when writing this), ini pun untuk beli domain diskon di Godaddy :D.

Dan untuk GCP nya, saya tidak ada kena charge, karena masih free trial, dan akan mendapat $300, jika sudah habis, maka segala domain dan aplikasi ini mungkin tidak dapat diakses lagi eheh :D, kecuali ada yang mau support long-term untuk biayanya😈

# Sample Project and Plan

*So..* Plannya adalah saya akan membuat sebuah aplikasi sederhana yang menggunakan Golang. Aplikasinya hanya berupa *Twitter API* , jadi hanya akan ada satu *resource* ***REST***

```
/tweets
```

Dalam kasus nyata, tentunya kita akan bekerja di dalam tim. Dimana akan terdapat 2 tim secara umum, yakni *frontend* dan *backend* . *Frontend* akan mengerjakan aplikasi berdasarkan *layout* dan data yang disediakan *backend* . Dan *backend* akan membuat API-nya.

Maka agar tidak ada *blocking* antar 2 tim ini, alangkah lebih baik jika kita pertama kali mendefine API Docs dan *specification* -nya. Dan untuk *frontend* akan lebih baik jika kita juga menyediakan mock server yang merepresentasikan *real* API kita nantinya.

Untuk kasus yang saya buat ini, saya hanya akan membuat API saja, tidak sampai implementasi *frontend* :D. Namun, jika ada teman *frontend* yang mau membantu, sangat dipersilahkan. Silahkan mengikuti artikel ini, dan hubungi saya, untuk di tambahkan sebagai next-part dari project Kube-Xmas :D

## Open API 3 Specification

Nah kembali ke topik, saya sudah mendefine API Docs yang akan dipakai oleh *frontend* dan *backend* . Saya menggunakan OpenAPI 3 sebagai standarnya. Informasi jelas tentang OpenAPI 3 dapat dilihat [disini](https://swagger.io/docs/specification/about/)

Nah, dari *API Specification* ini kita akan membuat API simple dari Golang yang di *deploy* ke *Kubernetes* .

Namun sebelum ke real implementasi di Golang, untuk mempermudah tim *frontend* juga, saya akan membuat mock server yang merepresentasikan API dari *backend* nantinya.

Tapi sebelum ke implementasi *mock server* , hal pertama saya lakukan adalah membuat men- *setup Kubernetes Cluster* saya di *Google Cloud* , yang akan saya bahas di tulisan part selanjutnya mengenai Project Kube-Xmas ini.

## Next:

*   [**Membuat *Kubernetes Cluster* di Google Cloud**](https://medium.com/easyread/membuat-kubernetes-cluster-di-google-cloud-ae14bee317ba)