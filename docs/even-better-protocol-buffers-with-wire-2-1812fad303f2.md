# 利用 Wire 2 实现更好的协议缓冲器

> 原文：<https://medium.com/square-corner-blog/even-better-protocol-buffers-with-wire-2-1812fad303f2?source=collection_archive---------1----------------------->

## 新的 Wire 版本可以缩小您的模式。

*由* [撰写*杰西·威尔逊*T5*。*](https://medium.com/u/dee2b4f5bec4?source=post_page-----1812fad303f2--------------------------------)

> 注意，我们已经行动了！如果您想继续了解 Square 的最新技术内容，请访问我们的新家[https://developer.squareup.com/blog](https://developer.squareup.com/blog)

我们已经在 [Wire](https://github.com/square/wire/) 上取得了稳定的进展，这是我们为 Android 和 Java 实现的紧凑、不可变的协议缓冲区。今天，我很高兴地宣布 Wire 2.0，它进一步完善了我们对协议缓冲区的理解。

Wire 2 中我最喜欢的特性之一是我们复杂的模式修剪。假设您有一个由 event.proto 定义的分析 API:

```
message Event {
  optional int64 created_at = 1;
  optional string message = 2;
  optional string url = 3;
  optional Browser browser = 4;
}message Browser {
  optional string user_agent = 1;
  repeated Feature features = 2; enum Feature {
    WEBGL = 1;
    LOCAL_STORAGE = 2;
    WEB_SQL = 3;
  }
}
```

事件上的浏览器字段对 Android 应用程序来说既没用也没必要。但是因为事件类型依赖于它，所以浏览器类和特性枚举都被拖到生成的代码中。未使用的代码会减慢构建速度，扩大 APK，并分散周围有用代码的注意力。

Wire 2 可以使用 includes 和 excludes 编译器选项删除不需要的垃圾。我们可以删除出现在任何地方的浏览器类型:

```
<plugin>
  <groupId>com.squareup.wire</groupId>
  <artifactId>wire-maven-plugin</artifactId> ... <excludes>
      <exclude>squareup.events.Browser</exclude>
    </excludes> ...</plugin>
```

或者仅出现这种情况:

```
<plugin>
  <groupId>com.squareup.wire</groupId>
  <artifactId>wire-maven-plugin</artifactId> ... <excludes>
      <exclude>squareup.events.Event#browser</exclude>
    </excludes> ...</plugin>
```

无论哪种情况，我们都会得到一个更适合使用它的客户机的事件消息。提供白名单和黑名单标识符，Wire 2 将保留匹配的项目及其可传递的依赖关系。

使用修剪来隐藏较新客户端的旧的、不推荐使用的内容，而服务保留所有内容以实现向后兼容性。或者对快速移动的客户团队隐藏新的、不稳定的字段！

Wire 2 的 [changelog](https://github.com/square/wire/blob/master/CHANGELOG.md) 列出了所有的新内容(这是实质性的)！将[线 2.0.0 接到 GitHub](https://github.com/square/wire/) 上。

[](/@swankjesse) [## 杰西·威尔逊

### 安卓和笑话。

medium.com](/@swankjesse)