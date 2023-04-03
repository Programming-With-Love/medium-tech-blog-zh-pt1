# Android 11 存储常见问题

> 原文：<https://medium.com/androiddevelopers/android-11-storage-faq-78cefea52b7c?source=collection_archive---------0----------------------->

![](img/de14acf6439dcd8e0639d02dd4c04976.png)

*Illustration by* [*Molly Hensley*](https://dribbble.com/Molly_Hensley)

在 Android 10 中首次引入的[范围存储](https://developer.android.com/training/data-storage#scoped-storage)旨在保护应用程序和用户数据，并减少文件混乱。从那以后，您提供了许多有价值的反馈，帮助我们改进了该功能，谢谢您。基于您的反馈，Android 11 包括几个显著的增强功能。例如，我们已经启用了对媒体文件的[直接文件路径访问](https://developer.android.com/preview/privacy/storage#media-direct-file-native)，以提高现有代码和库的兼容性。我们知道，许多应用程序，尤其是像 [Viber](https://android-developers.googleblog.com/2020/07/bringing-modern-storage-to-vibers-users.html) 这样的复杂应用程序，需要经过深思熟虑的规划来采用范围存储，以便继续支持现有用户，确保遵守当前的存储[最佳实践](https://developer.android.com/training/data-storage/use-cases)，并保持向后兼容性。根据与开发人员的对话和公共论坛上的热烈讨论，我们准备了一个常见问题，以帮助您更好地了解作用域存储中的各种功能、行为变化和限制。

# 常见问题解答

## 作用域存储是否允许应用程序使用文件路径访问文件，例如使用文件 API？

*   我们认识到一些应用依赖于直接访问媒体文件路径的代码或库。因此，在 Android 11 上，具有读取外部存储权限的应用程序能够访问作用域存储环境中具有文件路径的文件。在 Android 10 设备上，这对于作用域存储环境中的应用程序不可用，除非它们通过设置 Android:requestlegacyexternalstarge 清单属性[退出](https://developer.android.com/training/data-storage/use-cases#opt-out-scoped-storage)。为了确保跨 Android 版本的连续性，如果你的应用程序针对 Android 10 或更高版本，你也应该选择退出。有关详细信息，请参见[范围存储最佳实践](https://developer.android.com/training/data-storage/use-cases#access-file-paths)。

## 与媒体存储 API 相比，文件路径访问的性能如何？

*   性能实际上取决于具体的用例。对于视频播放等顺序读取，文件路径访问可提供与媒体存储相当的性能。但是，对于随机读取和写入，使用文件路径可能会慢两倍。为了获得最快和最一致的读写速度，我们建议使用媒体商店 API。

## 我的应用程序需要广泛访问共享存储。存储访问框架是唯一可用的选项吗？

*   存储访问框架(SAF)确实是允许用户授予对目录和文件的访问权限的一个选项。但是，请注意，对于某些目录，比如根目录和 Android/data 目录，存在着[访问限制](https://developer.android.com/preview/privacy/storage#file-directory-restrictions)。虽然大多数需要存储访问的应用程序可以使用 SAF 或 Media Store API 等最佳实践，但可能会出现应用程序需要广泛访问共享存储或无法利用这些最佳实践高效访问的情况。对于这些情况，我们添加了 [MANAGE_EXTERNAL_STORAGE](https://developer.android.com/preview/privacy/storage#all-files-access) 权限，以允许访问外部存储上的所有文件，除了 Android/data 和 Android/obb 目录。要了解有关 Google Play 指南的更多信息，请阅读政策帮助中心的[更新政策](https://support.google.com/googleplay/android-developer/answer/9956427)。

## 哪些类别的应用应该请求 [MANAGE_EXTERNAL_STORAGE 权限](https://developer.android.com/preview/privacy/storage#all-files-access)？

*   MANAGE_EXTERNAL_STORAGE 权限适用于具有核心使用情形的应用程序，这些应用程序需要对设备上的文件进行广泛的访问，但无法使用限定范围的存储最佳实践有效地做到这一点。虽然不可能列举所有可能的使用案例，但一些使用案例包括文件管理器、备份和恢复、防病毒应用程序或生产力文件编辑应用程序。

## 使用存储访问框架需要 Google Play 政策批准吗？

*   存储访问框架从 Android 4.4 开始就在平台中了。通过存储访问框架访问文件可为用户提供更好的控制，因为用户参与挑选文件，不需要任何用户权限。Google Play 没有与其使用相关的政策。

## 与 Android 10 相比，在 Android 11 中使用存储访问框架有什么进一步的限制吗？

*   针对 Android 11 (API level 30)并使用存储访问框架的应用将不再能够授予对目录的访问权限，例如 SD 卡的根目录和下载目录。不考虑目标 SDK，Android 11 上的存储访问框架不能用于获得对 Android/data 和 Android/obb 目录的访问。[了解更多关于这些限制和测试行为方式的](https://developer.android.com/preview/privacy/storage#file-directory-restrictions)。

## 应用程序如何测试范围内的存储更改？

*   应用程序可以通过[这些兼容性标志](https://developer.android.com/preview/privacy/storage#test-scoped-storage)测试与直接文件路径访问或媒体商店 API 相关的范围存储行为。还有[另一个兼容性标志](https://developer.android.com/preview/privacy/storage#test-file-directory-access)来测试使用存储访问框架访问某些路径的限制。

## 作用域存储中的应用程序是否仅限于将文件写入其特定于应用程序的数据目录？

*   在作用域存储中，应用程序可以[向媒体商店收藏贡献媒体文件](https://developer.android.com/training/data-storage/shared/media#add-item)。Media Store 会根据文件类型将文件放入组织有序的文件夹中，如 DCIM、电影、下载等。对于所有这样的文件，应用程序也可以通过文件 API 继续访问。操作系统维护一个系统，将应用程序归属于每个媒体商店文件，因此应用程序可以读取/写入它们最初贡献给媒体商店的文件，而无需存储权限。

## 使用媒体存储区[数据列](https://developer.android.com/reference/android/provider/MediaStore.MediaColumns#DATA)有什么指导，因为它已经被否决了？

*   在 Android 10 上，作用域存储环境中的应用无法使用文件路径访问文件。为了与这种设计保持一致，我们当时弃用了数据列。根据您对使用现有本机代码或库的需求的反馈，Android 11 现在支持范围存储中应用程序的文件路径访问。因此，数据列实际上对某些场景很有用。对于媒体存储中的插入和更新，作用域存储中的应用应使用 DISPLAY_NAME 和 RELATIVE_PATH 列。他们不能再为此使用数据列。当读取磁盘上存在的文件的媒体存储条目时，数据列将具有有效的文件路径，该路径可用于文件 API 或 NDK 文件库。然而，应用程序应该准备好处理来自这些操作的任何文件 I/O 错误，并且不应该假设文件总是可用的。

## 对于选择脱离作用域存储的应用程序，它们何时必须与作用域存储兼容？

*   在运行 Android 11 或更高版本的设备上，应用程序一旦面向 Android 11 或更高版本，就会被放入范围存储中。

## 迁移我们当前存储在范围存储之外的数据的建议方法是什么？

*   preserveLegacyExternalStorage 标志允许应用程序在升级时保留传统存储访问权限，即使是针对 Android 11。但是请注意，在 Android 11 的新安装上，这个标志没有任何作用。在瞄准 Android 11 之前，请进行代码更改以适应作用域存储。[了解有关数据迁移最佳实践的更多信息](https://developer.android.com/training/data-storage/use-cases#maintain_access_to_the_legacy_storage_location_for_data_migration)。

## 考虑到一些软件包安装程序，如应用商店，需要访问它，Android/obb 目录有什么例外吗？

*   持有 [REQUEST_INSTALL_PACKAGES 权限](https://developer.android.com/reference/android/Manifest.permission#REQUEST_INSTALL_PACKAGES)的应用可以访问其他应用的 Android/obb 目录。

我们希望此常见问题解答对您规划采用作用域存储有用。请访问我们的[最佳实践文档](https://developer.android.com/training/data-storage/use-cases)了解更多信息。