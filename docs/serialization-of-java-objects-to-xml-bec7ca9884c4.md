# 使用 XMLEncoder/Decoder 将 Java 对象序列化为 XML

> 原文：<https://medium.com/edureka/serialization-of-java-objects-to-xml-bec7ca9884c4?source=collection_archive---------1----------------------->

![](img/3ad5115dfffc36b9f2b0220dfb41cb9a.png)

可以使用 XMLEncoder、XMLDecoder 将 Java 对象序列化为 XML。Java 对象序列化特性是在 JDK 1.1 中引入的。串行化将 Java 对象或 Java 对象的图形转换成字节数组，该数组可以存储在文件中或通过网络传输。

稍后，我们可以将这些字节转换回 Java 对象。所有这些都是使用 java.io.ObjectOutputStream 和 java.io.ObjectInputStream 类完成的。ObjectOutputStream 类提供了将 Java 对象的原始数据类型和图形写入 OutputStream 的方法。可以使用 ObjectInputStream 读取(重构)这些对象。

然而，这种 Java 对象序列化的方法存在一些问题。其中一些列举如下:

*   保存和恢复序列化对象的逻辑基于组成类的内部结构。在保存对象和检索对象之间对这些类的任何更改都可能导致反序列化过程失败。
*   可能会出现版本问题。如果使用该类的一个版本保存对象，但尝试使用该类的一个更新的不同版本对其进行反序列化，反序列化可能会失败。

我们可以将 Java 对象序列化为人类可读的 XML 文档，而不是将其序列化为二进制格式。

**项目结构:**

![](img/0a1e471220c72c84f267bb957e439141.png)

**XMLEncoder**

java.beans.XMLEncoder 的工作原理是克隆对象图并记录创建克隆所需的步骤。这样，XMLEncoder 就有了一个对象图的“工作副本”,它模拟了 XMLDecoder 解码文件的步骤。让我们看看如何使用 XMLEncoder 序列化 Java 对象。

下面给出的是 DVD 类，它有一个列表<movie>作为成员。</movie>

```
public class DVD { 
 private List movies=new ArrayList(); 
 public DVD(){}
 public List getMovies() {
  return movies;
 }
 public void setMovies(List movies) {
  this.movies = movies;
 } 
 public String toString(){
  String movies="";
  for(Movie movie:getMovies()){
   movies += movie.getName()+", ";
  }
  return movies; 
 } 
 }public class DVD { 
 private List movies=new ArrayList(); 
 public DVD(){}
 public List getMovies() {
  return movies;
 }
 public void setMovies(List movies) {
  this.movies = movies;
 } 
 public String toString(){
  String movies="";
  for(Movie movie:getMovies()){
   movies += movie.getName()+", ";
  }
  return movies; 
 } 
 }
```

电影类的成员包括名称、运行时间、导演、上映年份和演员。

```
public class Movie { 
 private String name;
 private int runtime;
 private String directors;
 private int released; 
 private String cast; 
 public Movie(){}public Movie(String name, int runtime, String directors,int released, String cast) {  
  this.name = name;
  this.runtime = runtime;
  this.directors = directors;
  this.released = released;
  this.cast = cast;
 }
 public String getName() {
  return name;
 }
 public void setName(String name) {
  this.name = name;
 }
 public int getRuntime() {
  return runtime;
 }
 public void setRuntime(int runtime) {
  this.runtime = runtime;
 }
 public String getDirectors() {
  return directors;
 }
 public void setDirectors(String directors) {
  this.directors = directors;
 }
 public int getReleased() {
  return released;
 }
 public void setReleased(int released) {
  this.released = released;
 }
 public String getCast() {
  return cast;
 }
 public void setCast(String cast) {
  this.cast = cast;
 }}
```

我们想保存构成列表<movie>的 DVD 对象。序列化 DVD 对象也需要序列化电影对象。</movie>

**将对象序列化为 XML**

SerializeToXML 类具有创建四个电影对象的 main 方法。将它们放入一个列表中，然后将该列表设置为 DVD 实例值。一旦有了要序列化的对象，我们就创建一个 XMLEncoder 实例，然后编写该对象并调用 Encoder 实例上的 close 方法。

```
public class SerializeToXML {private static final String SERIALIZED_FILE_NAME="dvd.xml";public static void main(String args[]){Movie bourneIndentity=new Movie("The Bourne Identity",119,"Doug Liman",2002,"Matt Damon, Franka Potente");
  Movie bourneSupermacy=new Movie("The Bourne Supremacy",108,"Paul Greengrass",2004,"Matt Damon, Franka Potente, Joan Allen");
  Movie bourneUltimatum=new Movie("The Bourne Ultimatum",115,"Paul Greengrass",2007,"Matt Damon, Edgar Ramirez, Joan Allen");
  Movie bourneLegacy=new Movie("The Bourne Legacy",135,"Tony Gilroy",2012,"Jeremy Renner, Rachel Weisz, Edward Norton");List moviesList=new ArrayList();
  moviesList.add(bourneIndentity);
  moviesList.add(bourneSupermacy);
  moviesList.add(bourneUltimatum);
  moviesList.add(bourneLegacy);DVD bourneSeries=new DVD();
  bourneSeries.setMovies(moviesList);XMLEncoder encoder=null;
  try{
  encoder=new XMLEncoder(new BufferedOutputStream(new FileOutputStream(SERIALIZED_FILE_NAME)));
  }catch(FileNotFoundException fileNotFound){
   System.out.println("ERROR: While Creating or Opening the File dvd.xml");
  }
  encoder.writeObject(bourneSeries);
  encoder.close();}}
```

在执行 SerializeToXML 类时，它会将 java 对象序列化为 dvd.xml 文件(在 Eclipse IDE 中，您可能需要刷新项目才能看到新创建的 dvd.xml 文件)

```
< ?xml version="1.0" encoding="UTF-8"?>
< java version="1.7.0_75" class="java.beans.XMLDecoder">
 < object class="co.edureka.DVD" id="DVD0">
  < void property="movies">
   < void method="add">
    < object class="co.edureka.Movie">
     < void property="cast">
      < string>Matt Damon, Franka Potente< /string>
     < /void>
     < void property="directors">
      < string>Doug Liman< /string>
     < /void>
     < void property="name">
      < string>The Bourne Identity< /string>
     < /void>
     < void property="released">
      < int>2002< /int>
     < /void>
     < void property="runtime">
      < int>119< /int>
     < /void>
    < /object>
   < /void>
   < void method="add">
    < object class="co.edureka.Movie">
     < void property="cast">
      < string>Matt Damon, Franka Potente, Joan Allen< /string>
     < /void>
     < void property="directors">
      < string>Paul Greengrass< /string>
     < /void>
     < void property="name">
      < string>The Bourne Supremacy< /string>
     < /void>
     < void property="released">
      < int>2004< /int>
     < /void>
     < void property="runtime">
      < int>108< /int>
     < /void>
    < /object>
   < /void>
   < void method="add">
    < object class="co.edureka.Movie">
     < void property="cast">
      < string>Matt Damon, Edgar Ramirez, Joan Allen< /string>
     < /void>
     < void property="directors">
      < string>Paul Greengrass< /string>
     < /void>
     < void property="name">
      < string>The Bourne Ultimatum< /string>
     < /void>
     < void property="released">
      < int>2007< /int>
     < /void>
     < void property="runtime">
      < int>115< /int>
     < /void>
    < /object>
   < /void>
   < void method="add">
    < object class="co.edureka.Movie">
     < void property="cast">
      < string>Jeremy Renner, Rachel Weisz, Edward Norton< /string>
     < /void>
     < void property="directors">
      < string>Tony Gilroy< /string>
     < /void>
     < void property="name">
      < string>The Bourne Legacy< /string>
     < /void>
     < void property="released">
      < int>2012< /int>
     < /void>
     < void property="runtime">
      < int>135< /int>
     < /void>
    < /object>
   < /void>
  < /void>
 < /object>
< /java>
```

**从 XML 反序列化对象**

为了从 XML 文件中获取 DVD 对象，我们将使用 java.beans.Decoder 类。

我们创建一个 XMLDecoder 实例并调用 readObject 方法。由于 readObject()返回一个对象类型，因此需要显式强制转换

```
public class DeserializeFromXML {	
	private static final String SERIALIZED_FILE_NAME="dvd.xml";

	public static void main(String[] args) {
		XMLDecoder decoder=null;
		try {
			decoder=new XMLDecoder(new BufferedInputStream(new FileInputStream(SERIALIZED_FILE_NAME)));
		} catch (FileNotFoundException e) {
			System.out.println("ERROR: File dvd.xml not found");
		}
		DVD bourneSeries=(DVD)decoder.readObject();
		System.out.println(bourneSeries);

	}
}
```

**输出**

![](img/36027070cbbaf69210eddfae3b68d8e1.png)

我们将 Java 对象序列化为 XML 文档，然后反序列化它以获得实际的 Java 对象。

注意:我们在 DVD 和 Movie 类中都有无参数构造函数。如果要序列化的对象的对象图中涉及的每个类都没有无参数构造函数，您将得到 Java . lang . instantiation exception。

如果您有兴趣亲自尝试该代码，请下载它:

【button leads form _ title = " Download Code " redirect _ URL = https://edu reka . wistia . com/medias/jqusavrxbs/Download？media _ file _ id = 79202719 course _ id = 44 button _ text = "下载代码"]

如果你想查看更多关于人工智能、Python、道德黑客等市场最热门技术的文章，你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=serialization-of-java-objects-to-xml-using-xmlencoder-decode)

请留意本系列中解释 Java 其他各方面的其他文章。

> *1。* [*面向对象编程*](/edureka/object-oriented-programming-b29cfd50eca0)
> 
> *2。*[*Java 中的继承*](/edureka/inheritance-in-java-f638d3ed559e)
> 
> *3。*[*Java 中的多态性*](/edureka/polymorphism-in-java-9559e3641b9b)
> 
> *4。*[*Java 中的抽象*](/edureka/java-abstraction-d2d790c09037)
> 
> *5。* [*Java 字符串*](/edureka/java-string-68e5d0ca331f)
> 
> *6。* [*Java 数组*](/edureka/java-array-tutorial-50299ef85e5)
> 
> *7。* [*Java 收藏*](/edureka/java-collections-6d50b013aef8)
> 
> *8。* [*Java 线程*](/edureka/java-thread-bfb08e4eb691)
> 
> *9。*[*Java servlet 简介*](/edureka/java-servlets-62f583d69c7e)
> 
> *10。* [*Servlet 和 JSP 教程*](/edureka/servlet-and-jsp-tutorial-ef2e2ab9ee2a)
> 
> *11。*[*Java 中的异常处理*](/edureka/java-exception-handling-7bd07435508c)
> 
> *12。* [*Java 教程*](/edureka/java-tutorial-bbdd28a2acd7)
> 
> *13。* [*Java 面试题*](/edureka/java-interview-questions-1d59b9c53973)
> 
> *14。*[Java 程序 ](/edureka/java-programs-1e3220df2e76)
> 
> 15。[*kot Lin vs Java*](/edureka/kotlin-vs-java-4f8653f38c04)
> 
> *16。* [*依赖注入使用 Spring Boot*](/edureka/what-is-dependency-injection-5006b53af782)
> 
> *17。* [*堪比爪哇*](/edureka/comparable-in-java-e9cfa7be7ff7)
> 
> 18。 [*十大 Java 框架*](/edureka/java-frameworks-5d52f3211f39)
> 
> 19。 [*Java 反射 API*](/edureka/java-reflection-api-d38f3f5513fc)
> 
> 20。[*Java 中的 30 大模式*](/edureka/pattern-programs-in-java-f33186c711c8)
> 
> *21。* [*核心 Java 备忘单*](/edureka/java-cheat-sheet-3ad4d174012c)
> 
> *22。*[*Java 中的套接字编程*](/edureka/socket-programming-in-java-f09b82facd0)
> 
> *23。* [*Java OOP 备忘单*](/edureka/java-oop-cheat-sheet-9c6ebb5e1175)
> 
> *24。*[*Java 中的注释*](/edureka/annotations-in-java-9847d531d2bb)
> 
> *25。*[*Java 中的库管理系统项目*](/edureka/library-management-system-project-in-java-b003acba7f17)
> 
> *26。*[*Java 中的树木*](/edureka/java-binary-tree-caede8dfada5)
> 
> *27。*[*Java 中的机器学习*](/edureka/machine-learning-in-java-db872998f368)
> 
> *28。* [*顶级数据结构&Java 中的算法*](/edureka/data-structures-algorithms-in-java-d27e915db1c5)
> 
> *29。* [*Java 开发者技能*](/edureka/java-developer-skills-83983e3d3b92)
> 
> *30。* [*前 55 名 Servlet 面试问题*](/edureka/servlet-interview-questions-266b8fbb4b2d)
> 
> *31。*[](/edureka/java-exception-handling-7bd07435508c)*[*顶级 Java 项目*](/edureka/java-projects-db51097281e3)*
> 
> *32。 [*Java 字符串备忘单*](/edureka/java-string-cheat-sheet-9a91a6b46540)*
> 
> *33。[*Java 中的嵌套类*](/edureka/nested-classes-java-f1987805e7e3)*
> 
> *34。 [*Java 集合面试问答*](/edureka/java-collections-interview-questions-162c5d7ef078)*
> 
> **35。*[*Java 中如何处理死锁？*](/edureka/deadlock-in-java-5d1e4f0338d5)*
> 
> *36。 [*你需要知道的 50 个 Java 合集面试问题*](/edureka/java-collections-interview-questions-6d20f552773e)*
> 
> **37。*[*Java 中的字符串池是什么概念？*](/edureka/java-string-pool-5b5b3b327bdf)*
> 
> *38。[*C、C++和 Java 有什么区别？*](/edureka/difference-between-c-cpp-and-java-625c4e91fb95)*
> 
> **39。*[*Java 中的回文——如何检查一个数字或字符串？*](/edureka/palindrome-in-java-5d116eb8755a)*
> 
> *40。 [*你需要知道的顶级 MVC 面试问答*](/edureka/mvc-interview-questions-cd568f6d7c2e)*
> 
> *41。[*Java 编程语言的十大应用*](/edureka/applications-of-java-11e64f9588b0)*
> 
> **42。*[*Java 中的死锁*](/edureka/deadlock-in-java-5d1e4f0338d5)*
> 
> **43。*[*Java 中的平方和平方根*](/edureka/java-sqrt-method-59354a700571)*
> 
> **44。*[*Java 中的类型转换*](/edureka/type-casting-in-java-ac4cd7e0bbe1)*
> 
> **45。*[*Java 中的运算符及其类型*](/edureka/operators-in-java-fd05a7445c0a)*
> 
> **46。*[*Java 中的析构函数*](/edureka/destructor-in-java-21cc46ed48fc)*
> 
> **47。*[*Java 中的二分搜索法*](/edureka/binary-search-in-java-cf40e927a8d3)*
> 
> **48。*[*Java 中的 MVC 架构*](/edureka/mvc-architecture-in-java-a85952ae2684)*
> 
> **49。* [*冬眠面试问答*](/edureka/hibernate-interview-questions-78b45ec5cce8)*

**原载于 2021 年 9 月 30 日 ww.edureka.co**[*。*](https://www.edureka.co/blog/java-thread/)**