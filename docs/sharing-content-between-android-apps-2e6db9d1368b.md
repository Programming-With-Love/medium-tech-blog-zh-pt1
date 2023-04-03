# 在 Android 应用程序之间共享内容

> 原文：<https://medium.com/androiddevelopers/sharing-content-between-android-apps-2e6db9d1368b?source=collection_archive---------4----------------------->

![](img/77bf159d98cdb20e4cb302158ad92e2f.png)

正如他们所说，分享是关爱，但在 Android 上分享的意义可能略有不同。“共享”实际上是在应用程序之间发送文本、格式化文本、文件或图像等内容的简称。

因此，如果“共享”==发送内容，那么使用 [*ACTION_SEND*](http://developer.android.com/reference/android/content/Intent.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog#ACTION_SEND) (或[*ACTION _ SEND _ MULTIPLE*](http://developer.android.com/reference/android/content/Intent.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog#ACTION_SEND_MULTIPLE))意图和它的十几个额外功能来实现就更有意义了。

虽然这种方法非常有效，但我更喜欢使用 [*ShareCompat*](http://developer.android.com/reference/android/support/v4/app/ShareCompat.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog) ，这是 [v4 支持库](http://developer.android.com/tools/support-library/features.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog#v4)中的一组类，旨在简化构建共享内容的意图。

# 共享文本

可以想象，共享纯文本是一个很好的起点。事实上，这没什么大不了的:

```
Intent shareIntent = ShareCompat.IntentBuilder.from(activity)
  .setType("text/plain")
  .setText(shareText)
  .getIntent();
if (shareIntent.resolveActivity(getPackageManager()) != null) {
  startActivity(shareIntent);
}
```

[*ShareCompat。IntentBuilder* 对于共享，最重要的部分之一是选择正确的 mime 类型——这是应用程序过滤它们可以接收的内容类型的方式。通过使用*文本/纯文本*，我们表示我们的*意图*将只包含纯文本。然后，当然，](http://developer.android.com/reference/android/support/v4/app/ShareCompat.IntentBuilder.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog) [*setText()*](http://developer.android.com/reference/android/support/v4/app/ShareCompat.IntentBuilder.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog#setText(java.lang.CharSequence)) 就是我们实际上如何将 *CharSequence* 添加到意图发送中。虽然你当然可以使用 *setText()* 发送样式化的文本，但不能保证接收应用程序会接受这种样式，所以你应该确保无论有没有样式化，文本都清晰易读。

你会注意到我们在调用[*start activity()*](http://developer.android.com/reference/android/content/Context.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog#startActivity(android.content.Intent))之前使用了[*resolve activity()*](http://developer.android.com/reference/android/content/pm/PackageManager.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog#resolveActivity(android.content.Intent,%20int))。正如在[使用运行时检查保护隐式意图](https://www.youtube.com/watch?v=HGElAW224dE?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog)中所提到的，这对于在没有活动可用于处理您选择的 mime 类型时防止[*ActivityNotFoundException*](http://developer.android.com/reference/android/content/ActivityNotFoundException.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog)是至关重要的。虽然对于 *text/plain* 可能没有那么多问题，但它对于其他类型可能更常见。

> **注意:**当您使用 startActivity(shareIntent)时，会考虑用户设置的任何默认应用程序(即，如果他们之前选择了将所有“文本/普通”项目共享到某个应用程序)。如果你想总是显示一个消歧选择器，使用从[intent builder . createchooserintent()](http://developer.android.com/reference/android/support/v4/app/ShareCompat.IntentBuilder.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog#createChooserIntent())生成的意图，如 [ACTION_CHOOSER 文档](http://developer.android.com/reference/android/content/Intent.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog#ACTION_CHOOSER)中所述。

# 共享 HTML 文本

一些应用程序，尤其是电子邮件客户端，也支持 HTML 格式。与纯文本相比，这些变化相当小:

```
Intent shareIntent = ShareCompat.IntentBuilder.from(activity)
  .setType("text/html")
  .setHtmlText(shareHtmlText)
  .setSubject("Definitely read this")
  .addEmailTo(importantPersonEmailAddress)
  .getIntent();
```

这里的不同之处在于，我们使用了 [*setHtmlText()*](http://developer.android.com/reference/android/support/v4/app/ShareCompat.IntentBuilder.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog#setHtmlText(java.lang.String)) 来代替 *setText()* 以及一个 mime 类型的 *text/html* 来代替 *text/plain* 。这里 *ShareCompat* 实际上做了一点额外的事情: *setHtmlText()* 还使用[*html . from html()*](http://developer.android.com/reference/android/text/Html.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog#fromHtml(java.lang.String))来创建一个回退格式的文本，如果你自己以前没有调用过 *setText()* 的话，它将传递给接收应用程序。

鉴于许多可以接收 HTML 文本的应用程序都是电子邮件客户端，有许多帮助方法可以将主题设置为:，抄送:，密件抄送:电子邮件地址，考虑在任何共享意图中至少添加一个主题，以实现与电子邮件应用程序的最佳兼容性。

当然，您仍然需要像以前一样调用*resolve activity()*——没有任何变化。

# 接收文本

虽然到目前为止，我们关注的是发送端，但是了解另一端到底发生了什么是很有帮助的(如果不仅仅是构建一个简单的接收应用程序来安装在您的仿真器上进行测试的话)。接收活动将一个[意图过滤器](http://developer.android.com/guide/components/intents-filters.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog)添加到活动中:

```
<activity android:name=”.ShareActivity”>
  <intent-filter>
    <action android:name=”android.intent.action.SEND”/>
    <category android:name=”android.intent.category.DEFAULT”/>
    <category android:name=”android.intent.category.BROWSABLE”/>
    <data android:mimeType=”text/plain”/>
  </intent-filter>
</activity>
```

动作显然是更关键的部分——如果没有它，就没有什么能表明这是一个*动作 _ 发送*(分享背后的动作)。mime 类型，与我们的发送代码一样，也出现在这里。不太明显的是这两个类别。从[的<类>元素文档](http://developer.android.com/guide/topics/manifest/category-element.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog):

> **注意:**为了接收隐式意图，您必须在意图过滤器中包含 CATEGORY_DEFAULT 类别。startActivity()和 startActivityForResult()方法将所有意图视为声明了 CATEGORY_DEFAULT 类别。如果您没有在意图过滤器中声明它，那么没有隐含的意图会解析到您的活动中。

所以我们的用例需要 *CATEGORY_DEFAULT* 。然后，[*CATEGORY _ BROWSABLE*](http://developer.android.com/reference/android/content/Intent.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog#CATEGORY_BROWSABLE)允许[网页本地共享到应用](https://paul.kinlan.me/sharing-natively-on-android-from-the-web?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog)中，而不需要接收端的任何额外工作。

而要真正从意图中提取信息，有用的 [*ShareCompat。可以使用 IntentReader*](http://developer.android.com/reference/android/support/v4/app/ShareCompat.IntentReader.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog) :

```
ShareCompat.IntentReader intentReader =
    ShareCompat.IntentReader.from(activity);
if (intentReader.isShareIntent()) {
  String[] emailTo = intentReader.getEmailTo();
  String subject = intentReader.getSubject();
  String text = intentReader.getHtmlText();
  // Compose an email
}
```

与 *IntentBuilder* 类似， *IntentReader* 只是一个简单的包装器，使得提取信息变得容易。

# 共享文件和图像

虽然发送和接收文本足够简单(创建文本，将其包含在*意图*中)，但发送文件(尤其是图像——目前最常见的类型)还有一个额外的难题:文件权限。

您可以尝试的最简单的代码可能如下所示

```
File imageFile = ...;
Uri uriToImage = ...; // Convert the File to a UriIntent shareIntent = ShareCompat.IntentBuilder.from(activity)
  .setType("image/png")
  .setStream(uriToImage)
  .getIntent();
```

这几乎是可行的——棘手的部分是获得其他应用程序实际可以读取的*文件*的 *Uri* ，特别是当涉及 Android 6.0 棉花糖设备和[运行时权限](http://developer.android.com/training/permissions/requesting.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog)(包括现在危险的[*READ _ EXTERNAL _ STORAGE*](http://developer.android.com/reference/android/Manifest.permission.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog#READ_EXTERNAL_STORAGE)和[*WRITE _ EXTERNAL _ STORAGE*](http://developer.android.com/reference/android/Manifest.permission.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog#WRITE_EXTERNAL_STORAGE)权限)。

我的恳求:**不要用 *Uri.fromFile()*** 。它强制接收应用程序具有 *READ_EXTERNAL_STORAGE* 权限，如果你试图在用户之间[共享，它根本不起作用，而且在 KitKat 之前，会要求你的应用程序具有 *WRITE_EXTERNAL_STORAGE* 。而真正重要的共享目标，如](http://developer.android.com/training/enterprise/app-compatibility.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog#sharing_files) [Gmail](https://play.google.com/store/apps/details?id=com.google.android.gm?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog) ，没有 *READ_EXTERNAL_STORAGE* 权限——所以只会失败。

相反，你可以使用 [URI 权限](http://developer.android.com/guide/topics/security/permissions.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog#uri)来授予其他应用访问特定 URI 的权限。虽然 URI 权限不能在由 *Uri.fromFile()* 生成的 *file://* URIs 上工作，但是它们可以在与[内容提供商](http://developer.android.com/guide/topics/providers/content-providers.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog)相关联的 Uri 上工作。你**可以并且应该使用**[***File provider***](http://developer.android.com/reference/android/support/v4/content/FileProvider.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog)，而不是仅仅为此实现你自己的。

一旦设置好，我们的代码就变成了:

```
File imageFile = ...;
Uri uriToImage = **FileProvider.getUriForFile**(
    context, FILES_AUTHORITY, imageFile);Intent shareIntent = ShareCompat.IntentBuilder.from(activity)
  .setStream(uriToImage)
  .getIntent();
// Provide read access
shareIntent.setData(uriToImage);
shareIntent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);
```

使用[file provider . geturiforfile()](http://developer.android.com/reference/android/support/v4/content/FileProvider.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog#getUriForFile(android.content.Context,%20java.lang.String,%20java.io.File))，你将获得一个 *Uri* ，它实际上适合发送给另一个应用程序——他们将能够在没有任何存储权限的情况下读取它——相反，你使用[*FLAG _ GRANT _ READ _ URI _ PERMISSION*](http://developer.android.com/reference/android/content/Intent.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog#FLAG_GRANT_READ_URI_PERMISSION)专门授予他们读取权限。

> **注意:**在构建我们的 *ShareCompat 时，我们不会在任何地方调用 *setType()* (尽管在视频中我确实设置了)*。正如在[*setDataAndType()*Javadoc](http://developer.android.com/reference/android/content/Intent.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog#setDataAndType(android.net.Uri,%20java.lang.String))中所解释的，类型是使用 *getContentResolver()从数据 URI 中自动推断出来的。getType(uriToImage)* 。由于 *FileProvider* 自动返回正确的 mime 类型，我们根本不需要手动指定 mime 类型。

如果您有兴趣了解更多关于避免存储权限的信息，可以考虑观看我的[忘记存储权限讲座](https://www.youtube.com/watch?v=C28pvd2plBA?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog)或者至少浏览一下[幻灯片](https://speakerdeck.com/ianhanniballake/forget-the-storage-permission-alternatives-for-sharing-and-collaborating?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog)，在 [14:55](https://youtu.be/C28pvd2plBA?t=893&utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog) (幻灯片 11)中深入讨论了这个主题。

# 接收文件

接收文件和接收文本没有太大区别，因为你仍然要使用 ShareCompat。意图者。例如，要将一个传入的文件制作成一个*位图*，看起来应该是这样的:

```
Uri uri = ShareCompat.IntentReader.from(activity).getStream();
Bitmap bitmap = null;try {
  // Works with content://, file://, or android.resource:// URIs
  InputStream inputStream =
      getContentResolver().openInputStream(uri);
  bitmap = BitmapFactory.decodeStream(inputStream);
} catch (FileNotFoundException e) {
  // Inform the user that things have gone horribly wrong
}
```

当然，您可以自由地使用 InputStream 做任何您想做的事情——注意那些太大的图像，以至于遇到了 *OutOfMemoryException* 。你所知道的关于[加载位图](http://developer.android.com/training/displaying-bitmaps/load-bitmap.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog)的所有事情仍然适用。

# 支持库是你的朋友

有了 [*ShareCompat*](http://developer.android.com/reference/android/support/v4/app/ShareCompat.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog) (及其 [*IntentBuilder*](http://developer.android.com/reference/android/support/v4/app/ShareCompat.IntentBuilder.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog) 和 [*IntentReader*](http://developer.android.com/reference/android/support/v4/app/ShareCompat.IntentReader.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog) )和 [v4 支持库](http://developer.android.com/tools/support-library/features.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog#v4)中的 [*FileProvider*](http://developer.android.com/reference/android/support/v4/content/FileProvider.html?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog) ，默认情况下，您将能够在您的应用程序中包括共享文本、HTML 文本和文件。

# BuildBetterApps

关注 [Android 开发模式集合](https://plus.google.com/collection/sLR0p?utm_campaign=android_series_sharingcontent_012116&utm_source=medium&utm_medium=blog)了解更多！

![](img/ede78edee0069962aa0daa7cc8c85f02.png)