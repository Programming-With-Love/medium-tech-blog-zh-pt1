# 哥们，我的 char[]呢？

> 原文：<https://medium.com/square-corner-blog/dude-wheres-my-char-f1eb223f8800?source=collection_archive---------1----------------------->

## 在 Android M 中查找 String.value

*作者写的*[](http://twitter.com/Piwai)**。**

> *注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)*

**这篇文章最初是内部邮件列表上的一个帖子，我认为 Square 之外的人也会感兴趣。**

*当 Android M preview 2 发布时，我开始收到解析堆转储时 [LeakCanary](http://squ.re/leakcanary) 崩溃的报告。LeakCanary 进入一个 String 对象的 char 数组来读取一个线程名，但是在 Android M 中这个 char 数组已经不存在了。*

# *我们挖吧*

*下面是 Android M 之前 String.java 的结构:*

```
***public** **final** **class** **String** **{**
  **private** **final** **char** value**[];**
  **private** **final** **int** offset**;**
  **private** **final** **int** count**;**
  **private** **int** hash**;**
  *// ...*
**}***
```

*这是 String.java 在 M:*

```
***public** **final** **class** **String** **{**
  **private** **final** **int** count**;**
  **private** **int** hash**;**
  *// ...*
**}***
```

*char[]去哪了？为了了解更多，让我们看看当我们连接两个字符串时会发生什么:*

```
*String baguette **=** flour **+** love**;***
```

*换句话说:*

```
*String baguette **=** flour**.**concat**(**love**);***
```

*String.concat()现在是一个本机方法:*

```
***public** **final** **class** **String** **{**
  **public** **native** String **concat(**String string**);**
  *// ...*
**}***
```

# *走向本土化*

*concat()在 [String.cc](https://android.googlesource.com/platform/art/+/android-m-preview-1/runtime/native/java_lang_String.cc) 中实现:*

```
***static** jstring **String_concat**(JNIEnv***** env, jobject java_this, jobject java_string_arg) {
  ScopedFastNativeObjectAccess soa(env);
  **if** (UNLIKELY(java_string_arg **==** **nullptr**)) {
    ThrowNullPointerException("string arg == null");
    **return** **nullptr**;
  }
  StackHandleScope**<**2**>** hs(soa.Self());
  Handle**<**mirror**::**String**>** string_this(hs.NewHandle(soa.Decode**<**mirror**::**String***>**(java_this)));
  Handle**<**mirror**::**String**>** string_arg(hs.NewHandle(soa.Decode**<**mirror**::**String***>**(java_string_arg)));
  **int32_t** length_this **=** string_this**->**GetLength();
  **int32_t** length_arg **=** string_arg**->**GetLength();
  **if** (length_arg **>** 0 **&&** length_this **>** 0) {
    mirror**::**String***** result **=** mirror**::**String**::**AllocFromStrings(soa.Self(), string_this, string_arg);
    **return** soa.AddLocalReference**<**jstring**>**(result);
  }
  jobject string_original **=** (length_this **==** 0) **?** java_string_arg : java_this;
  **return** **reinterpret_cast<**jstring**>**(string_original);
}*
```

*实际的串联是在 [mirror::String.cc](https://android.googlesource.com/platform/art/+/android-m-preview-1/runtime/mirror/string.cc) 中的 mirror::String::AllocFromStrings 中完成的:*

```
*String***** String**::**AllocFromStrings(Thread***** self, Handle**<**String**>** string, Handle**<**String**>** string2) {
  **int32_t** length **=** string**->**GetLength();
  **int32_t** length2 **=** string2**->**GetLength();
  gc**::**AllocatorType allocator_type **=** Runtime**::**Current()**->**GetHeap()**->**GetCurrentAllocator();
  SetStringCountVisitor **visitor**(length **+** length2);
  String***** new_string **=** Alloc**<**true**>**(self, length **+** length2, allocator_type, visitor);
  **if** (UNLIKELY(new_string **==** **nullptr**)) {
    **return** **nullptr**;
  }
  **uint16_t*** new_value **=** new_string**->**GetValue();
  memcpy(new_value, string**->**GetValue(), length ***** **sizeof**(**uint16_t**));
  memcpy(new_value **+** length, string2**->**GetValue(), length2 ***** **sizeof**(**uint16_t**));
  **return** new_string;
}*
```

*首先，它使用 [string-inl.h](https://android.googlesource.com/platform/art/+/android-m-preview-1/runtime/mirror/string-inl.h) 中的 Alloc 分配一个大小合适的新字符串:*

```
***template** **<bool** kIsInstrumented, **typename** PreFenceVisitor**>**
**inline** String***** String**::**Alloc(Thread***** self, **int32_t** utf16_length, gc**::**AllocatorType allocator_type,
                             **const** PreFenceVisitor**&** pre_fence_visitor) {
  **size_t** header_size **=** **sizeof**(String);
  **size_t** data_size **=** **sizeof**(**uint16_t**) ***** utf16_length;
  **size_t** size **=** header_size **+** data_size;
  Class***** string_class **=** GetJavaLangString();
  *// Check for overflow and throw OutOfMemoryError if this was an unreasonable request.*
  **if** (UNLIKELY(size **<** data_size)) {
    self**->**ThrowOutOfMemoryError(StringPrintf("%s of length %d would overflow",
                                             PrettyDescriptor(string_class).c_str(),
                                             utf16_length).c_str());
    **return** **nullptr**;
  }
  gc**::**Heap***** heap **=** Runtime**::**Current()**->**GetHeap();
  **return** down_cast**<**String***>**(
      heap**->**AllocObjectWithAllocator**<**kIsInstrumented, true**>**(self, string_class, size,
                                                            allocator_type, pre_fence_visitor));
}*
```

*   *这将分配一个大小为 header_size + data_size 的对象*
*   *header_size 是 [mirror::String.h](https://android.googlesource.com/platform/art/+/android-m-preview-1/runtime/mirror/string.h) 中 mirror::String 的大小:*
*   *int32_t 计数 _*
*   *uint32_t 哈希代码 _*
*   *uint16_t 值 _[0]*
*   *data_size 实际上是总字符数乘以一个字符的大小(uint16_t)。*

*这意味着字符数组被内联在字符串对象中。*

*还要注意零长度数组:uint16_t value_[0]。*

*我们继续读 mirror::String::AllocFromStrings:*

```
***uint16_t*** new_value **=** new_string**->**GetValue();
  memcpy(new_value, string**->**GetValue(), length ***** **sizeof**(**uint16_t**));
  memcpy(new_value **+** length, string2**->**GetValue(), length2 ***** **sizeof**(**uint16_t**));*
```

*其中 GetValue()在 [mirror::String.h](https://android.googlesource.com/platform/art/+/android-m-preview-1/runtime/mirror/string.h) 中定义，并返回我们上面注意到的 uint16_t value_[0]的地址:*

```
***uint16_t*** **GetValue**() SHARED_LOCKS_REQUIRED(Locks**::**mutator_lock_) {
  **return** **&**value_[0];
}*
```

*这是从一个数组的内存地址到另一个数组的非常简单的复制。*

# *抵消*

*您可能已经注意到，偏移场现在完全消失了。Java 字符串是不可变的，所以早期版本的 JDK 允许子字符串共享其父字符串的 char 数组，但偏移量和计数不同。这意味着保留一个小的子字符串可以在内存中保留一个更大的字符串，并防止它被垃圾收集。*

*char 数组现在内联在 String 对象中，所以子字符串不能共享它们的父 char 数组，这就是为什么不再需要 offset 了。*

# *优势*

*让我们推测一下为什么这些变化很有趣:*

*   *空间[引用位置](https://en.wikipedia.org/wiki/Locality_of_reference):char 数组就在字符串数据的其余部分旁边，而不是必须遵循引用并冒着使 CPU 缓存无效的风险。*
*   *更小的内存占用:Java char 数组包含一个头来存储它的类型和长度，这是多余的。*
*   *两个对象都必须是 4 字节对齐的，现在只有一个对象需要填充。*

*String 是 VM 中最常用的类型之一，所以这些微优化将会带来巨大的改进。*

# *结论:回到堆转储*

*因为 char[] value 字段已从 String.java 中删除，所以无法在堆转储中对其进行解析。然而在 Android M Preview 2 中，char 缓冲区仍然在堆转储中序列化，在字符串地址之后 16 个字节(因为字符串结构不长于 16 个字节)。这意味着我们可以让 LeakCanary 再次与 Android M 一起工作:*

```
*Object value **=** fieldValue**(**values**,** "value"**);**
ArrayInstance charArray**;**
**if** **(**isCharArray**(**value**))** **{**
  charArray **=** **(**ArrayInstance**)** value**;**
**}** **else** **{**
  charArray **=** **(**ArrayInstance**)** heap**.**getInstance**(**instance**.**getId**()** **+** 16**);**
**}***
```

*这个漏洞最终会在 Android M 中通过在转储堆时在所有字符串对象中插入一个虚拟 char[] valuefield 来修复[。](https://code.google.com/p/android-developer-preview/issues/detail?id=2769)*

*非常感谢[切斯特·谢](https://twitter.com/dunnodono)、[罗曼·盖伊](https://twitter.com/romainguy)、[杰西·威尔逊](https://twitter.com/jessewilson)和[杰克·沃顿](https://twitter.com/jakewharton)帮助解决这个问题。*

*[](http://twitter.com/Piwai) [## 皮埃尔-伊夫·里考(@皮瓦伊)|推特

### Pierre-Yves Ricau (@Piwai)的最新推文。安卓贝克@广场。巴黎/旧金山

twitter.com](http://twitter.com/Piwai)*