# Spring Mail

> 原文：<https://medium.easyread.co/spring-mail-9b5f4a890d25?source=collection_archive---------4----------------------->

## Mengirim Email Menggunakan Spring Mail

![](img/90d3b8f3d3e12744bae0f6d63ab102a1.png)

Photo by [Herrmann Stamm](https://unsplash.com/photos/fgxLd58HdF4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

*Email* atau surat elektronik adalah fitur yang umum yang tersedia pada sebuah aplikasi. Untuk itu pada artikel kali ini saya akan membahas mengenai salah satu fitur dari *spring* yang membantu kita sebagai *developer* untuk membuat aplikasi mengirim email, yaitu *spring mail* . Untuk menggunakan fitur spring email cukup mudah dengan menambahkan *dependency* **spring-boot-starter-mail.**

## **Step 1\. Menyiapkan pom.xml**

Untuk menggunakan spring mail pada aplikasi spring boot cukup tambahkan *dependency* berikut di pom.xml.

Pada pom diatas saya menambahkan *dependency* lombok untuk mempermudah membuat POJO.

## **Step 2\. Menyiapkan application.yml**

Ada banyak layanan surel yang bisa digunakan namun kali ini saya akan menggunakan Gmail.

> **Note** : Jika ingin menggunakan smtp gmail jangan lupa untuk nyalakan [**allow less secure apps**](https://myaccount.google.com/lesssecureapps) terlebih dahulu

## **Step 3\. Menyiapkan File Email (SendingEmail.java)**

Pada contoh kali ini saya akan menggunakan *simple* REST API untuk mengirimkan email.

## **Step 4\. Menyiapkan MailRequest.java and MailResponse.java**

Saya juga menambahkan DTO ( [Data Transfer Object](https://martinfowler.com/eaaCatalog/dataTransferObject.html) ) untuk request email dan juga response email

## **Step 6\. Menyiapkan EmailService.java**

1.  **MailSender interface** : MailSender adalah *root* *interface* . *Interface* ini menyediakan fungsi dasar untuk mengirim email.
2.  **JavaMailSender interface** : JavaMailSender adalah *subinterface* dari MailSender. JavaMailSender mendukung MIME *messages* . Kebanyakan digunakan dengan **MimeMessageHelper** class untuk membuat JavaMail **MimeMessage** , dengan attachment dll. Untuk spring sendiri direkomendasikan **MimeMessagePreparator** untuk menggunakan *interface* ini.
3.  **JavaMailSenderImpl class** : Class ini menyediakan implementasi dari JavaMailSender *interface* . JavaMailSenderImpl mendukung JavaMail MimeMessages dan Spring SimpleMailMessages.
4.  **SimpleMailMessage class** : Dapat digunakan untuk membuat mail sederhana yaitu from, to, cc, subject dan text messages.
5.  **MimeMessagePreparator interface** : MimeMessagePreparator adalah *callback* *interface* dari JavaMail MIME messages.
6.  **MimeMessageHelper class** : Class ini adalah *helper* *class* untuk membuat MIME message. class ini mendukung inline elements seperti images, typical mail attachments dan HTML text content.

Ada banyak class dan interface yang disediakan java untuk membantu mengirim email. Namun pada contoh kali ini saya hanya akan menggunakan MimeMessageHelper.

## **Step 7\. Run With Postman**

Untuk menjalankannya, gumakan postman dengan *request* berikut:

[Post http://localhost:8080/sendingEmail](http://localhost:8080/sendingEmail)

```
{
 "name": "test",
 "to" : "[to@gmail.com](mailto:sintongjait@gmail.com)",
 "from" : "[from@gmail.com](mailto:notessku@gmail.com)",
 "subject" : "test email"
}
```

Kira-kira begitulah cara mengirim email menggunakan spring mail. **Silahkan mencoba!!!**

# **Referensi**

[https://docs.spring.io/spring/docs/3.0.0.M3/reference/html/ch26s03.html](https://docs.spring.io/spring/docs/3.0.0.M3/reference/html/ch26s03.html)