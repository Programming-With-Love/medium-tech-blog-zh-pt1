# 深入研究 Java 瞬态修饰符

> 原文：<https://medium.com/google-developer-experts/diving-deeper-into-the-java-transient-modifier-3b16eff68f42?source=collection_archive---------4----------------------->

![](img/63900ad7385dc103976c4ea86298c222.png)

Nothing is tied forever. Neither are transient variables.

上周我发表了一篇文章来帮助你理解 Java 中的引用是如何工作的。它被广泛接受，我得到了很多建设性的反馈。这就是我热爱软件社区的原因。

今天我想给你看另一篇文章，深入到一个没有被广泛使用的话题:T2 瞬态 T3 修改器。就我个人而言，当我开始使用它时，我记得我能够很快掌握它的理论方面，尽管应用是一个不同性质的问题。让我们仔细检查一下

# 瞬变修改器

> 应用于字段的**瞬态**修饰符告诉 Java，当对象被序列化时，这个属性应该被排除。当对象被反序列化时，该字段将使用其默认值进行初始化(对于引用类型，这通常是一个 *null* 值，如果对象是基元类型，则为 *zero* / *false* )。

你会同意我的观点:理论很简单，但是我们最初没有看到实际的一面。我们应该在哪里应用瞬变修改器？什么时候会有用？除非你以前用过，否则很难想出一个真实的例子。就像狗追尾巴一样，我们找不到用例，因此我们不能将[实践应用于理论](/@enriquelopezmanas/the-theoretical-animal-4f6901aaf571#.w9db0bi8k)。

我写这篇文章的目的是帮助你打破这种恶性循环。让我们看几个实际的例子。

想想一个**用户**对象。该**用户**的所有属性中包含*登录*、*电子邮件*和*密码*。当数据被序列化并通过网络传输时，我们可以想到一些安全原因，为什么我们不愿意将字段*密码*与整个对象一起发送。在这种情况下，将字段标记为**瞬态**将解决这个安全问题。这在代码中会是什么样子？

```
@Data    
@NoArgsConstructor
@AllArgsConstructor
public class User implements Serializable {

    private static final long serialVersionUID = 123456789L;

    private String login;
    private String email;
    private transient String password;

    public void printInfo() {
        System.out.println("Login is: " + login);
        System.out.println("Email is: " + email);
        System.out.println("Password is: " + password);
    }
}
```

注意，这个对象正在实现接口 *Serializable* ，当您打算序列化一个对象时，这是必需的。如果这个接口没有实现，你会收到一个*NotSerializableException*。还要注意声明的字段 *serialVersionUID* 。如果您使用任何主要的开发环境或 Eclipse，它通常会被自动重新创建。

如果您现在序列化然后反序列化一个 User 类型的对象，那么值 password 将会是 null，因为它已经被标记为 transient。

> 看到注释@Data、@NoArgsConstructor 和@AllArgsConstructor？它们是由 [Lombok](https://projectlombok.org/) 提供的，这是一个让事情变得更简单的 Java 库。虽然在 2016 年 Lombok 不像以前那么有用(现在像 Kotlin 这样的语言会自动生成 setters 和 getters，在任何主要的开发环境和 Eclipse 中，你只需双击就可以做到这一点)，但我仍然喜欢在某些领域中使用它，以保持一个干净的领域模型集合。

当使用**瞬态**修饰符时，我还能想到另一个用例:当一个对象从另一个派生时。在这种情况下，我们可以通过将派生字段**转换为瞬态字段**来提高代码的效率。

让我们来看看这段代码:

```
@Data    
@NoArgsConstructor
@AllArgsConstructor
public class GalleryImage implements Serializable { private static final long serialVersionUID = 123456789L;

    private Image image;
    private transient Image thumbnailImage;

    private void generateThumbnail() {
        // This method will derive the thumbnail from the main image
    }

    private void readObject(ObjectInputStream inputStream)
            throws IOException, ClassNotFoundException {
        inputStream.defaultReadObject();
        generateThumbnail();
    }    
}
```

在这种情况下，该类包含一个主*图像*和一个*缩略图图像*字段。最新的字段将从前者派生而来。如果我们能让*thumbnail image***transient**成为我们的代码，我们会更有效率:当对象被序列化时，从另一个字段派生的字段将不会被传递。

文末的一个小观点:可以想象，一个**瞬态**变量不可能是**静态**(或者如果是也没有太大意义)。**静态**字段是隐式**瞬态**，不会被序列化。

> 摘要:当一个对象包含您不想传输的敏感数据，或者当它包含您可以从其他元素获得的数据时，使用 **transient** 。**静态**字段是隐式**瞬态**。

我在我的推特账户上写下我对软件工程和生活的想法。如果你喜欢这篇文章或者它确实帮助了你，请随意分享和/或留下评论。这是给业余作家加油的货币。