# 每个开发人员都应该知道的 13 个基本 JavaScript 数组技巧和诀窍

> 原文：<https://medium.com/duomly-blockchain-online-courses/13-essential-javascript-array-tips-and-tricks-every-developer-should-know-a9ef9fdc4a6a?source=collection_archive---------4----------------------->

![](img/e181d6470b6764179f88a2b8fe845ec5.png)

[Duomly — programming online courses](https://www.duomly.com)

本文原载:[https://www . blog . duomly . com/13-useful-JavaScript-array-tips-and-tricks-you-should-know/](https://www.blog.duomly.com/13-useful-javascript-array-tips-and-tricks-you-should-know/)

数组是 Javascript 最常见的概念之一，它为我们处理存储在其中的数据提供了很多可能性。考虑到数组是 Javascript 中最基本的主题之一，您在编程之初就要学习它，在本文中，我将向您展示一些您可能不知道的技巧，它们可能对编码非常有帮助！让我们开始吧。

# 1.从数组中删除重复项

关于 Javascript 数组，如何从 Javascript 数组中提取唯一值，这是一个很流行的面试问题。这里有一个快速简单的方法来解决这个问题，你可以使用一个新的 Set()来实现这个目的。我想向你们展示两种可能的方法，一种是。from()方法和带有扩展运算符(…)的 second。

```
var fruits = [“banana”, “apple”, “orange”, “watermelon”, “apple”, “orange”, “grape”, “apple”];// First methodvar uniqueFruits = Array.from(new Set(fruits));console.log(uniqueFruits); // returns [“banana”, “apple”, “orange”, “watermelon”, “grape”]// Second methodvar uniqueFruits2 = […new Set(fruits)];console.log(uniqueFruits2); // returns [“banana”, “apple”, “orange”, “watermelon”, “grape”]
```

很简单，对吧？

# 2.替换数组中的特定值

有时在创建代码时，需要替换数组中的特定值，有一个很好的简单方法可以做到这一点，但您可能还不知道。为此，我们可以使用。splice( *start，value to remove，valueToAdd* )并向其传递所有三个参数，指定我们希望从哪里开始修改，我们希望更改多少值以及新值。

```
var fruits = [“banana”, “apple”, “orange”, “watermelon”, “apple”, “orange”, “grape”, “apple”];fruits.splice(0, 2, “potato”, “tomato”);console.log(fruits); // returns [“potato”, “tomato”, “orange”, “watermelon”, “apple”, “orange”, “grape”, “apple”]
```

# 3.没有映射数组。地图()

大概大家都知道吧。map()方法，但是有一个不同的解决方案可以用来获得类似的效果和非常干净的代码。我们可以使用。from()方法。

```
var friends = [{ name: ‘John’, age: 22 },{ name: ‘Peter’, age: 23 },{ name: ‘Mark’, age: 24 },{ name: ‘Maria’, age: 22 },{ name: ‘Monica’, age: 21 },{ name: ‘Martha’, age: 19 },]var friendsNames = Array.from(friends, ({name}) => name);console.log(friendsNames); // returns [“John”, “Peter”, “Mark”, “Maria”, “Monica”, “Martha”]
```

# 4.清空数组

你是否有一个数组充满了元素但出于任何目的都需要清理它，你又不想一个一个的移除条目？只用一行代码就可以完成，非常简单。要清空一个数组，你需要设置数组的长度为 0，就这样！

```
var fruits = [“banana”, “apple”, “orange”, “watermelon”, “apple”, “orange”, “grape”, “apple”];fruits.length = 0;console.log(fruits); // returns []
```

# 5.将数组转换为对象

碰巧我们有一个数组，但出于某种目的，我们需要一个包含这些数据的对象，将数组转换为对象的最快方法是使用众所周知的 spread 运算符(…)。

```
var fruits = [“banana”, “apple”, “orange”, “watermelon”];var fruitsObj = { …fruits };console.log(fruitsObj); // returns {0: “banana”, 1: “apple”, 2: “orange”, 3: “watermelon”, 4: “apple”, 5: “orange”, 6: “grape”, 7: “apple”}
```

# 6.用数据填充数组

有些情况下，当我们创建一个数组，我们想用一些数据填充它，或者我们需要一个数组有相同的值，在这种情况下。fill()方法提供了一个简单明了的解决方案。

```
var newArray = new Array(10).fill(“1”);console.log(newArray); // returns [“1”, “1”, “1”, “1”, “1”, “1”, “1”, “1”, “1”, “1”, “1”]
```

# 7.合并数组

你知道如何将数组合并成一个数组吗？concat()方法？有一种简单的方法可以将任意数量的数组合并成一行代码。正如你可能已经意识到的，spread 运算符(…)在处理数组时非常有用，在这种情况下也是如此。

```
var fruits = [“apple”, “banana”, “orange”];var meat = [“poultry”, “beef”, “fish”];var vegetables = [“potato”, “tomato”, “cucumber”];var food = […fruits, …meat, …vegetables];console.log(food); // [“apple”, “banana”, “orange”, “poultry”, “beef”, “fish”, “potato”, “tomato”, “cucumber”]
```

# 8.求两个数组的交集

这也是你在任何 Javascript 面试中可能遇到的最普遍的挑战之一，因为它显示了你是否能使用数组方法，以及你的逻辑是什么。为了找到两个数组的交集，我们将使用本文中之前展示的方法之一，以确保我们正在检查的数组中的值没有重复，我们将使用。过滤方法和。包括方法。因此，将获得包含两个数组中都存在的值的数组。检查代码:

```
var numOne = [0, 2, 4, 6, 8, 8];var numTwo = [1, 2, 3, 4, 5, 6];var duplicatedValues = […new Set(numOne)].filter(item => numTwo.includes(item));console.log(duplicatedValues); // returns [2, 4, 6]
```

# 9.从数组中删除 falsy 值

首先，让我们定义虚假值。在 Javascript 中，falsy 值为 false、0、“”、null、NaN、undefined。现在我们可以知道如何从数组中删除这种值。为了实现这一点，我们将使用。filter()方法。

```
var mixedArr = [0, “blue”, “”, NaN, 9, true, undefined, “white”, false];var trueArr = mixedArr.filter(Boolean);console.log(trueArr); // returns [“blue”, 9, true, “white”]
```

# 10.从数组中获取随机值

有时我们需要从数组中随机选择一个值。为了以一种简单、快速、简短的方式创建它，并保持代码整洁，我们可以根据数组长度获得一个随机的索引号。让我们看看代码:

```
var colors = [“blue”, “white”, “green”, “navy”, “pink”, “purple”, “orange”, “yellow”, “black”, “brown”];var randomColor = colors[(Math.floor(Math.random() * (colors.length + 1)))]
```

# 11.反转数组

当我们需要翻转数组时，没有必要通过复杂的循环和函数来创建数组，有一个简单的数组方法可以为我们做所有的事情，只需一行代码，我们就可以让数组反转。让我们检查一下:

```
var colors = [“blue”, “white”, “green”, “navy”, “pink”, “purple”, “orange”, “yellow”, “black”, “brown”];var reversedColors = colors.reverse();console.log(reversedColors); // returns [“brown”, “black”, “yellow”, “orange”, “purple”, “pink”, “navy”, “green”, “white”, “blue”]
```

# 12.。lastIndexOf()方法

在 Javascript 中，有一个有趣的方法，它允许查找给定元素最后一次出现的索引。例如，如果我们的数组有重复的值，我们可以找到它最后出现的位置。让我们看看代码示例:

```
var nums = [1, 5, 2, 6, 3, 5, 2, 3, 6, 5, 2, 7];var lastIndex = nums.lastIndexOf(5);console.log(lastIndex); // returns 9
```

# 13.对数组中的所有值求和

Javascript 工程师面试中经常遇到的另一个挑战。没有可怕的东西来到这里；这个问题可以用。一行代码中的 reduce 方法。让我们来看看代码:

```
var nums = [1, 5, 2, 6];var sum = nums.reduce((x, y) => x + y);console.log(sum); // returns 14
```

# 结论

在本文中，我向您介绍了 13 个技巧和提示，可以帮助您编写代码，并保持代码简洁明了。此外，请记住，在 Javascript 中有许多不同的技巧值得探索，不仅是关于数组，还有不同的数据类型。我希望您喜欢文章中提供的解决方案，并且您将使用它们来改进您的开发过程。要了解更多关于数组和其他 Javascript 特性的信息，请参加我们的 [Javascript 课程。](https://www.duomly.com/course/javascript-course)

好好编码！

![](img/85efcae8b6643e89b35d82c58cd17686.png)

[Duomly — programming online courses](https://www.duomly.com)

感谢您的阅读。

本文由我们的队友安娜提供。