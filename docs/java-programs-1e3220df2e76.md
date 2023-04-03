# Java 程序——了解适合初学者的最佳 Java 程序

> 原文：<https://medium.com/edureka/java-programs-1e3220df2e76?source=collection_archive---------0----------------------->

![](img/061a96f557e438d3bfc4e4b1f64fb17d.png)

Java Programs — Edureka

Java 是最流行的编程语言之一，广泛应用于 IT 行业。它简单、健壮，有助于我们重用代码。在本文中，让我们看看一些重要的程序来理解 **Java 基础知识。**

下面是我将在本文中涉及的程序列表。

**基础 Java 程序**

1.  Java 中的计算器程序
2.  使用递归的阶乘程序
3.  斐波那契数列程序
4.  Java 中的回文程序
5.  排列组合程序
6.  Java 中的模式程序
7.  Java 中的字符串反向程序
8.  Java 中的镜像逆程序

**高级 Java 程序**

1.  Java 中的二分搜索法程序
2.  Java 堆排序程序
3.  从 ArrayList 中移除元素
4.  Java 中的 HashMap 程序
5.  Java 中的循环链表程序
6.  Java 数据库连接程序
7.  矩阵程序的转置

我们开始吧！

# 基本 Java 程序

## 1.编写一个 Java 程序来执行基本的计算器操作。

当你想到计算器时，像加、减、乘、除这样的运算就会浮现在脑海里。让我们在下面程序的帮助下实现基本的计算器操作。

```
package Edureka;
import java.util.Scanner;
public class Calculator {
public static void main(String[] args) {
Scanner reader = new Scanner(System.in);
System.out.print("Enter two numbers: ");
// nextDouble() reads the next double from the keyboard
double first = reader.nextDouble();
double second = reader.nextDouble();
System.out.print("Enter an operator (+, -, *, /): ");
char operator = reader.next().charAt(0);
double result;
//switch case for each of the operations
switch(operator)
{
case '+':
result = first + second;
break;
case '-':
result = first - second;
break;
case '*':
result = first * second;
break;
case '/':
result = first / second;
break;
// operator doesn't match any case constant (+, -, *, /)

default:
System.out.printf("Error! operator is not correct");
return;
}
//printing the result of the operations
System.out.printf("%.1f %c %.1f = %.1f", first, operator, second, result);
}
}
```

当您执行上述程序时，输出如下所示:

```
Enter two numbers: 20 98
Enter an operator (+, -, *, /): /
20.0 / 98.0 = 0.2
```

## 2.写一个 Java 程序来计算一个数的阶乘。

一个数的阶乘是所有小于或等于该数的正数的乘积。一个数 n 的阶乘用 n 表示！

现在，让我们写一个程序，用递归求出一个数的阶乘。

```
package Edureka;
import java.util.Scanner;
public class Factorial {
public static void main(String args[]){
//Scanner object for capturing the user input
Scanner scanner = new Scanner(System.in);
System.out.println("Enter the number:");
//Stored the entered value in variable
int num = scanner.nextInt();
//Called the user defined function fact
int factorial = fact(num);
System.out.println("Factorial of entered number is: "+factorial);
}
static int fact(int n)
{
int output;
if(n==1){
return 1;
}
//Recursion: Function calling itself!!
output = fact(n-1)* n;
return output;
}
}
```

在执行上述程序时，您将得到一个数的阶乘，如下所示:

```
Enter the number:
12
Factorial of entered number is: 47900160
```

## 3.写一个 Java 程序，计算最多 n 个数的斐波那契数列。

这是一个级数，其中下一项是前两项之和。比如:0 1 1 2 3 5 8 13……让我们写一个 Java 程序来计算斐波那契数列。

```
package Edureka;
public class Fibonacci {
public static void main(String[] args) {
//initializing the constants
int n = 100, t1 = 0, t2 = 1;
System.out.print("Upto " + n + ": ");
//while loop to calculate fibonacci series upto n numbers
while (t1<= n)
{
System.out.print(t1 + " + ");
int sum = t1 + t2;
t1 = t2;
t2 = sum;
}
}
}
```

在执行上述代码时，输出如下所示:

```
Upto 100: 0 + 1 + 1 + 2 + 3 + 5 + 8 + 13 + 21 + 34 + 55 + 89 +
```

## 4.写一个 Java 程序，找出给定的字符串是否是回文。

回文是一个数字、字符串或序列，即使你颠倒了顺序，它们还是一样的。例如，赛车，如果反过来拼写，将与赛车相同。

```
package Edureka;
import java.util.Scanner;
public class Palindrome {
static void checkPalindrome(String input) {
//Assuming result to be true
boolean res = true;
int length = input.length();
//dividing the length of the string by 2 and comparing it.
for(int i=0; i<= length/2; i++) {
if(input.charAt(i) != input.charAt(length-i-1)) {
res = false;
break;
}
}
System.out.println(input + " is palindrome = "+res);
}
public static void main(String[] args) {
Scanner sc = new Scanner(System.in);
System.out.print("Enter your Statement: ");
String str = sc.nextLine();
//function call
checkPalindrome(str);
}
}
```

当您运行代码时，它将检查给定的字符串是否是回文，如下所示:

```
Enter your Statement: RACECAR
RACECAR is palindrome = true

Enter your Statement: EDUREKA
EDUREKA is palindrome = false
```

## 5.写一个 Java 程序计算 2 个数的排列组合。

它是给定数量的元素一个接一个、一部分或全部的不同排列。我们来看看它的实现。

```
package Edureka;
import java.util.Scanner;
public class nprandncr {
//calculating a factorial of a number
public static int fact(int num)
{
int fact=1, i;
for(i=1; i<=num; i++)
{
fact = fact*i;
}
return fact;
}
public static void main(String args[])
{
int n, r;
Scanner scan = new Scanner(System.in);
System.out.print("Enter Value of n : ");
n = scan.nextInt();
System.out.print("Enter Value of r : ");
r = scan.nextInt();
// NCR and NPR of a number
System.out.print("NCR = " +(fact(n)/(fact(n-r)*fact(r))));
System.out.print("nNPR = " +(fact(n)/(fact(n-r))));
}
}
```

执行上述代码时，输出如下所示:

```
Enter Value of n : 5
Enter Value of r : 3
NCR = 10
NPR = 60
```

## 6.用 Java 写一个程序找出字母表和菱形图案。

在这里，您可以使用 for 循环来打印 Java 中的各种模式。在本文中，我将实现两种不同的模式。第一个将是 ***字母 A 图案*** ，下一个将是 ***菱形图案。*** 现在让我们看看字母表 A 模式的实现。

```
package Edureka;
import java.util.Scanner;
public class PatternA {
// Java program to print alphabet A pattern
void display(int n)
{
// Outer for loop for number of lines
for (int i = 0; i<=n; i++) {
// Inner for loop for logic execution
for (int j = 0; j<= n / 2; j++) {
// prints two column lines
if ((j == 0 || j == n / 2) && i != 0 ||
// print first line of alphabet
i == 0  && j != n / 2 ||
// prints middle line
i == n / 2)
System.out.print("*");
else
System.out.print(" ");
}
System.out.println();
}
}
public static void main(String[] args)
{
Scanner sc = new Scanner(System.in);
PatternA a = new PatternA();
a.display(7);
}
}
```

**输出:**

![](img/40bec6d2d7543c4ad771eeb40dab58de.png)

```
package Edureka;
import java.util.Scanner;
public class DiamondPattern
{
public static void main(String args[])
{
int n, i, j, space = 1;
System.out.print("Enter the number of rows: ");
Scanner s = new Scanner(System.in);
n = s.nextInt();
space = n - 1;
for (j = 1; j<= n; j++)
{
for (i = 1; i<= space; i++)
{
System.out.print(" ");
}
space--;
for (i = 1; i <= 2 * j - 1; i++)
{
System.out.print("*");
}
System.out.println("");
}
space = 1;
for (j = 1; j<= n - 1; j++)
{
for (i = 1; i<= space; i++)
{
System.out.print(" ");
}
space++;
for (i = 1; i<= 2 * (n - j) - 1; i++)
{
System.out.print("*");
}
System.out.println("");
}
}
}
```

**输出:**

```
Enter the number of rows: 5

    *
   ***
  *****
 *******
*********
 *******
  *****
   ***
    *
```

这将是菱形图案程序的输出。现在让我们进一步看看接下来会发生什么。

## 7.写一个 Java 程序来反转给定字符串中的字母。

由用户输入。例如， *Java* 程序反转出现在*字符串中的字母* **Hello People** 将被称为 **olleH elpoeP。**让我们用 Java 实现同样的功能。

```
package Edureka;
public class stringreverse {
public static void main(String[] args) {
// TODO Auto-generated method stub
String str = "Welcome To Edureka";
String[] strArray = str.split(" ");
for (String temp: strArray){
System.out.println(temp);
}
for(int i=0; i<3; i++){ char[] s1 = strArray[i].toCharArray(); for (int j = s1.length-1; j>=0; j--)
{System.out.print(s1[j]);}
System.out.print(" ");
}
}
}
```

上述程序的输出如下所示:

```
Welcome
To
Edureka
emocleW oT akerudE
```

## 8.写一个 Java 程序来检查给定的数组是否镜像逆。

一个**数组**叫做**镜像**—**逆**如果它的**逆**等于它本身。现在让我们写一个程序，检查给定的数组是否是镜像逆的。

```
package Edureka;
//Java implementation of the approach
public class MirrorInverse {
// Function that returns true if
// the array is mirror-inverse
static boolean isMirrorInverse(int arr[])
{
for (int i = 0; i<arr.length; i++) {
// If condition fails for any element
if (arr[arr[i]] != i)
return false;
}
// Given array is mirror-inverse
return true;
}

public static void main(String[] args)
{
int arr[] = { 1, 2, 3, 0 };
if (isMirrorInverse(arr))
System.out.println("Yes");
else
System.out.println("No");
}
}
```

**输出:否**

//如果给定的数组是{3，4，2，0，1}，那么它将输出 yes，因为该数组是镜像反转的。

# 高级 Java 程序

## 1.写一个 Java 程序来实现二分搜索法算法。

这是一个**搜索**算法，用于查找目标值在排序数组中的位置。**二分搜索法将目标值与数组的中间元素进行比较**。现在让我们看看如何实现二分搜索法算法。

```
package Edureka1;
public class BinarySearch {
// Java implementation of recursive Binary Search
// Returns index of x if it is present in arr[l..
// r], else return -1
int binarySearch(int arr[], int l, int r, int x)
{
if (r >= l) {
int mid = l + (r - l) / 2;
// If the element is present at the
// middle itself
if (arr[mid] == x)
return mid;
// If element is smaller than mid, then
// it can only be present in left subarray
if (arr[mid] >x)
return binarySearch(arr, l, mid - 1, x);
// Else the element can only be present
// in right subarray
return binarySearch(arr, mid + 1, r, x);
}
// We reach here when element is not present
// in array
return -1;
}
public static void main(String args[])
{
BinarySearch ob = new BinarySearch();
int arr[] = { 2, 3, 4, 10, 40 };
int n = arr.length;
int x = 40;
int result = ob.binarySearch(arr, 0, n - 1, x);
if (result == -1)
System.out.println("Element not present");
else
System.out.println("Element found at index " + result);
}
}
```

在执行上述程序时，它将定位出现在特定索引处的元素

```
Element found at index 4
```

## 2.写一个 Java 程序实现堆排序算法。

*堆排序*是一种基于二进制堆数据结构的比较排序技术。这类似于选择排序，我们首先找到最大值元素，并将最大值元素放在最后。然后对其余元素重复相同的过程。让我们编写程序并理解它的工作原理。

```
package Edureka1;
public class HeapSort
{
public void sort(int arr[])
{
int n = arr.length;
// Build heap (rearrange array)
for (int i = n / 2 - 1; i >= 0; i--)
heapify(arr, n, i);
// One by one extract an element from heap
for (int i=n-1; i>=0; i--)
{
// Move current root to end
int temp = arr[0];
arr[0] = arr[i];
arr[i] = temp;
// call max heapify on the reduced heap
heapify(arr, i, 0);
}
}
void heapify(int arr[], int n, int i)
{
int largest = i; // Initialize largest as root
int l = 2*i + 1; // left = 2*i + 1
int r = 2*i + 2; // right = 2*i + 2
// If left child is larger than root
if (l< n && arr[l] >arr[largest])
largest = l;
// If right child is larger than largest so far
if (r < n && arr[r] > arr[largest])
largest = r;

// If largest is not root
if (largest != i)
{
int swap = arr[i];
arr[i] = arr[largest];
arr[largest] = swap;
// Recursively heapify the affected sub-tree
heapify(arr, n, largest);
}
}
/* A utility function to print array of size n */
static void printArray(int arr[])
{
int n = arr.length;
for (int i=0; i<n; ++i)
System.out.print(arr[i]+" ");
System.out.println();
}
// Driver program
public static void main(String args[])
{
int arr[] = {12, 11, 13, 5, 6, 7};
int n = arr.length;
HeapSort ob = new HeapSort();
ob.sort(arr);
System.out.println("Sorted array is");
printArray(arr);
}
}
```

**输出:**

```
5,6,7,11,12,13
```

## 3.写一个 Java 程序从数组列表中移除元素

ArrayList 是 List 接口的实现，可以动态地在列表中添加或删除元素。此外，如果添加的元素超过初始大小，列表的大小会动态增加。在下面的程序中，我首先将元素插入数组列表，然后根据规范从列表中删除元素。让我们来理解代码。

```
package Edureka1;

import java.util.ArrayList;
import java.util.List;
import java.util.function.Predicate;

public class ArrayListExample {
public static void main(String[] args) {
List<String> programmingLanguages = new ArrayList<>();
programmingLanguages.add("C");
programmingLanguages.add("C++");
programmingLanguages.add("Java");
programmingLanguages.add("Kotlin");
programmingLanguages.add("Python");
programmingLanguages.add("Perl");
programmingLanguages.add("Ruby");

System.out.println("Initial List: " + programmingLanguages);

// Remove the element at index `5`
programmingLanguages.remove(5);
System.out.println("After remove(5): " + programmingLanguages);

// Remove the first occurrence of the given element from the ArrayList
// (The remove() method returns false if the element does not exist in the ArrayList)
boolean isRemoved = programmingLanguages.remove("Kotlin");
System.out.println("After remove(\"Kotlin\"): " + programmingLanguages);

// Remove all the elements that exist in a given collection
List<String> scriptingLanguages = new ArrayList<>();
scriptingLanguages.add("Python");
scriptingLanguages.add("Ruby");
scriptingLanguages.add("Perl");

programmingLanguages.removeAll(scriptingLanguages);
System.out.println("After removeAll(scriptingLanguages): " + programmingLanguages);

// Remove all the elements that satisfy the given predicate
programmingLanguages.removeIf(new Predicate<String>() {
[@Override](http://twitter.com/Override)
public boolean test(String s) {
return s.startsWith("C");
}
});

System.out.println("After Removing all elements that start with \"C\": " + programmingLanguages);

// Remove all elements from the ArrayList
programmingLanguages.clear();
System.out.println("After clear(): " + programmingLanguages);
}
}
```

程序执行时的输出如下所示:

```
Initial List: [C, C++, Java, Kotlin, Python, Perl, Ruby]
After remove(5): [C, C++, Java, Kotlin, Python, Ruby]
After remove("Kotlin"): [C, C++, Java, Python, Ruby]
After removeAll(scriptingLanguages): [C, C++, Java]
After Removing all elements that start with "C": [Java]
After clear(): []
```

## 4.用 Java 写一个程序实现 HashMap。

**HashMap** 是一个基于映射的集合类，用于存储键&值对，表示为 **HashMap** <键，值>或 **HashMap** < K，V >。这个类不保证地图的顺序。它类似于 Hashtable 类，只是它是不同步的，并且允许空值(空值和空键)。让我们看看如何借助下面的程序用 Java 实现 HashMap 逻辑。

```
package Edureka1;

import java.util.HashMap;
import java.util.Map;

public class Hashmap
{
public static void main(String[] args)
{
HashMap<String, Integer> map = new HashMap<>();
print(map);
map.put("abc", 10);
map.put("mno", 30);
map.put("xyz", 20);

System.out.println("Size of map is" + map.size());

print(map);
if (map.containsKey("abc"))
{
Integer a = map.get("abc");
System.out.println("value for key \"abc\" is:- " + a);
}
map.clear();
print(map);
}
public static void print(Map<String, Integer> map)
{
if (map.isEmpty())
{
System.out.println("map is empty");
}
else
{
System.out.println(map);
}
}
}
```

在执行 HashMap 程序时，输出如下:

```
map is empty
Size of map is:- 3
{abc=10, xyz=20, mno=30}
value for key "abc" is:- 10
map is empty
```

## 5.编写一个 Java 程序来打印循环链表中的节点

它遵循先做第一件事的方法。这里，节点是**列表**的一个元素，它有两个部分，data 和 next。Data 表示存储在节点中的数据，next 是指向下一个节点的指针。现在让我们来理解它的实现。

```
package Edureka1;

public class CircularlinkedList {
//Represents the node of list.
public class Node{
int data;
Node next;
public Node(int data) {
this.data = data;
}
}
//Declaring head and tail pointer as null.
public Node head = null;
public Node tail = null;

//This function will add the new node at the end of the list.
public void add(int data){
//Create new node
Node newNode = new Node(data);
//Checks if the list is empty.
if(head == null) {
//If list is empty, both head and tail would point to new node.
head = newNode;
tail = newNode;
newNode.next = head;
}
else {
//tail will point to new node.
tail.next = newNode;
//New node will become new tail.
tail = newNode;
//Since, it is circular linked list tail will point to head.
tail.next = head;
}
}

//Displays all the nodes in the list
public void display() {
Node current = head;
if(head == null) {
System.out.println("List is empty");
}
else {
System.out.println("Nodes of the circular linked list: ");
do{
//Prints each node by incrementing pointer.
System.out.print(" "+ current.data);
current = current.next;
}while(current != head);
System.out.println();
}
}

public static void main(String[] args) {
CircularlinkedList cl = new CircularlinkedList();
//Adds data to the list
cl.add(1);
cl.add(2);
cl.add(3);
cl.add(4);
//Displays all the nodes present in the list
cl.display();
}
}
```

执行该程序时，输出如下所示:

```
Nodes of the circular linked list:
1 2 3 4
```

## 6.编写一个连接到 SQL 数据库的 Java 程序。

JDBC 是一个标准的 Java API，用于 Java 编程语言和各种数据库之间的独立于数据库的连接。这个应用程序接口允许您用结构化查询语言(SQL)对访问请求语句进行编码。然后，它们被传递给管理数据库的程序。它主要包括打开一个连接，创建一个 SQL 数据库，执行 SQL 查询，然后到达输出。让我们看一个创建数据库、建立连接和运行查询的示例代码。

```
package Edureka1;
import java.sql.*;
import java.sql.DriverManager;
public class Example {
// JDBC driver name and database URL
static final String JDBC_DRIVER = "com.mysql.jdbc.Driver";
static final String DB_URL = "jdbc:mysql://localhost/emp";
// Database credentials
static final String USER = "root";
static final String PASS = "edureka";
public static void main(String[] args) {
Connection conn = null;
Statement stmt = null;
try{
//STEP 2: Register JDBC driver
Class.forName("com.mysql.cj.jdbc.Driver");
//STEP 3: Open a connection
System.out.println("Connecting to database...");
conn = DriverManager.getConnection(DB_URL,"root","edureka");
//STEP 4: Execute a query
System.out.println("Creating statement...");
stmt = conn.createStatement();
String sql;
sql = "SELECT id, first, last, age FROM Employees";
ResultSet rs = stmt.executeQuery(sql);
//STEP 5: Extract data from result set
while(rs.next()){
//Retrieve by column name
int id = rs.getInt("id");
int age = rs.getInt("age");
String first = rs.getString("first");
String last = rs.getString("last");
//Display values
System.out.print("ID: " + id);
System.out.print(", Age: " + age);
System.out.print(", First: " + first);
System.out.println(", Last: " + last);
}
//STEP 6: Clean-up environment
rs.close();
stmt.close();
conn.close();
}catch(SQLException se){
//Handle errors for JDBC
se.printStackTrace();
}catch(Exception e){
//Handle errors for Class.forName
e.printStackTrace();
}finally{
//finally block used to close resources
try{
if(stmt!=null)
stmt.close();
}catch(SQLException se2){
}// nothing can be done
try{
if(conn!=null)
conn.close();
}catch(SQLException se){
se.printStackTrace();
}//end finally try
}//end try
System.out.println("Goodbye!");
}//end main
} // end Example
```

在执行上述代码时，它将建立到数据库的连接，并检索数据库中的数据。

```
Connecting to database...
Creating statement...
ID: 100, Age: 18, First: Zara, Last: Ali
ID: 101, Age: 25, First: Mahnaz, Last: Fatma
ID: 102, Age: 30, First: Zaid, Last: Khan
ID: 103, Age: 28, First: Sumit, Last: Mittal
Goodbye!
```

## 7.写一个 Java 程序，求给定矩阵的转置。

矩阵的转置是通过将行变为列，将列变为行来实现的。换句话说，A[][]的转置是通过将 A[i][j]变为 A[j][i]而获得的。

```
package Edureka1;

public class Transpose
{
static final int N = 4;

// This function stores transpose
// of A[][] in B[][]
static void transpose(int A[][], int B[][])
{
int i, j;
for (i = 0; i< N; i++)
for (j = 0; j <N; j++)
B[i][j] = A[j][i];
}

public static void main (String[] args)
{
int A[][] = { {1, 1, 1, 1},
{2, 2, 2, 2},
{3, 3, 3, 3},
{4, 4, 4, 4}};

int B[][] = new int[N][N], i, j;

transpose(A, B);

System.out.print("Result matrix is n");
for (i = 0; i<N; i++)
{
for (j = 0; j<N; j++)
System.out.print(B[i][j] + " ");
System.out.print("n");
}
}
}
```

在执行上述程序时，输出如下:

```
Result matrix is
1 2 3 4
1 2 3 4
1 2 3 4
1 2 3 4
```

如果您在使用这些 java 程序时遇到任何挑战，请在下面的部分评论您的问题。除了这篇 Java 程序文章之外，如果您想从专业人士那里获得这方面的培训，您可以选择 Edureka 的结构化培训！

这就把我们带到了 Java 程序博客的结尾。我希望您发现它提供了很多信息，并帮助您理解了 Java 基础知识。

如果你想查看更多关于人工智能、DevOps、道德黑客等市场最热门技术的文章，那么你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=java-programs)

请留意本系列中的其他文章，它们将解释 Java 的各个方面。

> 1.[面向对象编程](/edureka/object-oriented-programming-b29cfd50eca0)
> 
> 2.[Java 中的继承](/edureka/inheritance-in-java-f638d3ed559e)
> 
> 3.[Java 中的多态性](/edureka/polymorphism-in-java-9559e3641b9b)
> 
> 4.[Java 中的抽象](/edureka/java-abstraction-d2d790c09037)
> 
> 5. [Java 字符串](/edureka/java-string-68e5d0ca331f)
> 
> 6. [Java 数组](/edureka/java-array-tutorial-50299ef85e5)
> 
> 7. [Java 集合](/edureka/java-collections-6d50b013aef8)
> 
> 8. [Java 线程](/edureka/java-thread-bfb08e4eb691)
> 
> 9.[Java servlet 简介](/edureka/java-servlets-62f583d69c7e)
> 
> 10. [Servlet 和 JSP 教程](/edureka/servlet-and-jsp-tutorial-ef2e2ab9ee2a)
> 
> 11.[Java 中的异常处理](/edureka/java-exception-handling-7bd07435508c)
> 
> 12.[高级 Java 教程](/edureka/advanced-java-tutorial-f6ebac5175ec)
> 
> 13. [Java 面试问题](/edureka/java-interview-questions-1d59b9c53973)
> 
> 14. [Java 教程](/edureka/java-tutorial-bbdd28a2acd7)
> 
> 15.[科特林 vs Java](/edureka/kotlin-vs-java-4f8653f38c04)
> 
> 16.[依赖注入使用 Spring Boot](/edureka/what-is-dependency-injection-5006b53af782)
> 
> 17.[Java 中可比的](/edureka/comparable-in-java-e9cfa7be7ff7)
> 
> 18.[十大 Java 框架](/edureka/java-frameworks-5d52f3211f39)
> 
> 19. [Java 反射 API](/edureka/java-reflection-api-d38f3f5513fc)
> 
> 20.[Java 中的 30 大模式](/edureka/pattern-programs-in-java-f33186c711c8)
> 
> 21.[核心 Java 备忘单](/edureka/java-cheat-sheet-3ad4d174012c)
> 
> 22.[Java 中的套接字编程](/edureka/socket-programming-in-java-f09b82facd0)
> 
> 23. [Java OOP 备忘单](/edureka/java-oop-cheat-sheet-9c6ebb5e1175)
> 
> 24.[Java 中的注释](/edureka/annotations-in-java-9847d531d2bb)
> 
> 25.[Java 中的图书管理系统项目](/edureka/library-management-system-project-in-java-b003acba7f17)
> 
> 26.[Java 中的树](/edureka/java-binary-tree-caede8dfada5)
> 
> 27.[Java 中的机器学习](/edureka/machine-learning-in-java-db872998f368)
> 
> 28.[顶级数据结构&Java 中的算法](/edureka/data-structures-algorithms-in-java-d27e915db1c5)
> 
> 29. [Java 开发者技能](/edureka/java-developer-skills-83983e3d3b92)
> 
> 30.[前 55 名 Servlet 面试问题](/edureka/servlet-interview-questions-266b8fbb4b2d)
> 
> 31. [](/edureka/java-exception-handling-7bd07435508c) [顶级 Java 项目](/edureka/java-projects-db51097281e3)
> 
> 32. [Java 字符串备忘单](/edureka/java-string-cheat-sheet-9a91a6b46540)
> 
> 33.[Java 中的嵌套类](/edureka/nested-classes-java-f1987805e7e3)
> 
> 34. [Java 集合面试问答](/edureka/java-collections-interview-questions-162c5d7ef078)
> 
> 35.[Java 中如何处理死锁？](/edureka/deadlock-in-java-5d1e4f0338d5)
> 
> 36.[你需要知道的 50 大 Java 集合面试问题](/edureka/java-collections-interview-questions-6d20f552773e)
> 
> 37.[Java 中的字符串池是什么概念？](/edureka/java-string-pool-5b5b3b327bdf)
> 
> 38.[C、C++和 Java 有什么区别？](/edureka/difference-between-c-cpp-and-java-625c4e91fb95)
> 
> 39.[Java 中的回文——如何检查一个数字或字符串？](/edureka/palindrome-in-java-5d116eb8755a)
> 
> 40.[你需要知道的顶级 MVC 面试问答](/edureka/mvc-interview-questions-cd568f6d7c2e)
> 
> 41.[Java 编程语言的十大应用](/edureka/applications-of-java-11e64f9588b0)
> 
> 42.[Java 中的死锁](/edureka/deadlock-in-java-5d1e4f0338d5)
> 
> 43.[Java 中的平方和平方根](/edureka/java-sqrt-method-59354a700571)
> 
> 44.[Java 中的类型转换](/edureka/type-casting-in-java-ac4cd7e0bbe1)
> 
> 45.[Java 中的运算符及其类型](/edureka/operators-in-java-fd05a7445c0a)
> 
> 46.[Java 中的析构函数](/edureka/destructor-in-java-21cc46ed48fc)
> 
> 47.[Java 中的二分搜索法](/edureka/binary-search-in-java-cf40e927a8d3)
> 
> 48.[Java 中的 MVC 架构](/edureka/mvc-architecture-in-java-a85952ae2684)
> 
> 49. [Hibernate 面试问答](/edureka/hibernate-interview-questions-78b45ec5cce8)

*原载于 2019 年 6 月 11 日*[*【https://www.edureka.co】*](https://www.edureka.co/blog/java-programs/)*。*