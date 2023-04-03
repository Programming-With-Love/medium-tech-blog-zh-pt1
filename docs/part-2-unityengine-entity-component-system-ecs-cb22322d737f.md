# Part 2: UnityEngine Entity Component System (ECS)

> 原文：<https://medium.easyread.co/part-2-unityengine-entity-component-system-ecs-cb22322d737f?source=collection_archive---------7----------------------->

## Entity-Component-System Series

## UnityEngine Build-in ECS

**UnityEngine** adalah contoh *game* *engine* yang menggunakan **ECS** untuk pengembangan aplikasi di atasnya. Pada UnityEngine, konsep ECS diperkenalkan dengan istilah yang berbeda, yaitu :
- **Entity** direpresentasikan dengan **GameObject
- Component** direpresentasikan dengan **MonoBehaviour
- System** direpresentasikan dengan **MonoBehaviour**

Selain penggunaan istilah yang berbeda, hal yang lebih penting menurut saya adalah UnityEngine tidak *strict* (tidak memaksa) *developer* untuk mengikuti ECS *pattern* . Mungkin, hal ini dilakukan tim UnityEngine untuk menghasilkan *framework* yang fleksibel. Namun disisi lain, ke- *fleksibel-an* justru membuat UnityEngine menjadi semakin sulit untuk digunakan ke *project* medium dan besar, *project* yang membutuhkan kolaborasi beberapa *developer* , karena memungkinkan banyak cara melakukan 1 hal sesuai dengan persepsi *developer* , sehingga kemungkinan *pattern* yang tidak konsisten pada 1 *project* .

## MonoBehaviour Problem

**Unity Mono Behaviour** adalah bagian yang paling saya sukai ketika belajar menggunakan Unity. *MonoBehaviour* sangat mudah digunakan dan di pelajari. Tetapi semakin mendalam, saya menemukan banyak problem pada *MonoBehaviour* yang teman-teman juga bisa cari di Google, beberapa diantaranya yaitu:

**Anti-Rename** *MonoBehaviour* sangat sulit untuk diubah namanya. Mungkin terkesan sepele, tetapi selama proses *development* , teman-teman akan sering mengganti *sub-type* dari *MonoBehaviour* . Ketika *sub-type* *MonoBehaviour* telah di- *assign* ke sebuah *GameObject* , lalu kita merubah namanya, maka akan mengakibatkan *null component* pada *GameObject* tersebut. Hal ini sulit untuk dideteksi, terlebih ketika sudah memiliki banyak *GameObject* atau *prefab* .

**Sulit untuk di test.** *MonoBehaviour* adalah tipe yang harus di- *assign* ke *GameObject* . *Nah* , tentu saja hal ini akan mempersulit proses pembuatan *mock* *object* dari *sub-type* *MonoBehaviour* untuk melakukan *testing* .

**Tidak CPU 缓存友好**。
这是 ECS 的第一个主题，它是 CPU 缓存友好的*，它通过使用*缓存*组件*来提高性能。 *Nah，*Hal this did dukung oleh*mono behavior*karena ia memiliki banyak data/*property*yang akan di-*inherit*ke*sub type*nya。**

***单一行为迟缓。** Fungsi-fungsi 在*monobhavior*比如`**Start()**`、`**Update()**`、`**LateUpdate()**` memiliki *开销*将在*unit engine*内部完成。如果我们把这些功能换成手动的，会更快。*

*但是，我并不认为我们不了解单行为 T31 或单行为 T32，也不认为我们了解单行为 T33 或单行为 T34。世界卫生组织致力于改善单一行为*。**

## *ECS unity engine 的替代方案*

*在达成一致的基础上，我同意建立一个更加严格的框架来实施 ECS。Ada beberapa yang saya temukan yaitu:*

*[**斯维尔托。ECS (github)**](https://github.com/sebas77/Svelto.ECS)
*面向 c#和 Unity 的真实实体-组件-系统(也可以适配其他 c#平台)。能够轻松编写封装的、非耦合的、高效的、面向数据的、缓存友好的、多线程的代码。斯维尔托。ECS 是一个框架，有了它，你不得不按照它的规范来设计你的代码。它是一个框架，规定了你编写游戏基础设施的方式。该框架也相对僵化，迫使编码人员遵循一个定义良好的模式。**

*[**Entitas cs harp(github)**](https://github.com/sschmid/Entitas-CSharp)
*Entitas 是专门为 C#和 Unity 打造的超快速实体组件系统框架(ECS)。内部缓存和极快的组件访问速度使其首屈一指。已经做出了几个设计决策，以便在垃圾收集环境中最佳地工作，并且在垃圾收集器上更加容易。Entitas 附带了一个可选的代码生成器，它从根本上减少了您必须编写的代码量，并且* [*使您的代码读起来像写得很好的散文。*](https://cleancoders.com/)*

*[**BrokenBricksECS(github)**](https://github.com/Spy-Shifty/BrokenBricksECS)
BrokenBricksECS 是继 [Unite Austin 2017 —编写高性能 C#脚本(Youtube)](https://www.youtube.com/watch?v=tGmnZdY5Y-E)unity 3d 团队的 Joachim Ante 关于新 UnityEngine ECS 和 JobSystem 框架的演示之后开发的框架。*

*研究结束后，我的儿子比以前更有希望了。ECS 将 say guna 在 say studio 与 alas berikut:
1。斯维尔托。ECS 框架 API yang lebih*readable*and terstruktur
2。斯维尔托。ECS 不支持单一行为。斯维尔托。ECS mendukung *CPU 缓存友好*。
4。斯维尔托。ECS mendukung 纯 C#*

# *关闭*

**不*，从我们第一次使用 Unity 内置 ECS*开始*我们就有了另一种选择，而且我说的是 Svelto.ECS。ECS pada UnityEngine*

*第二部分，东谷下一步-nya ya:)*

**Artikel ini di tulis oleh*[*Leo Pripos mar bun*](https://medium.com/u/ea9a874c360d?source=post_page-----cb22322d737f--------------------------------)*beliau adalah 联合创始人 Dan CEO Dari*[*Ned Studio*](http://nedstudio.net/)*sebuah 创业游戏开发迪日惹。**

*如果这份文件是伪造的，他们就可以用这份文件。*