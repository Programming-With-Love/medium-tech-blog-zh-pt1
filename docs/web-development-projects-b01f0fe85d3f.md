# 你必须实践的顶级 Web 开发项目

> 原文：<https://medium.com/edureka/web-development-projects-b01f0fe85d3f?source=collection_archive---------3----------------------->

![](img/65304ebc812fd84b166fc3792b23c2ed.png)

据 TechRepublic 报道，网页开发是 2019 年 10 大热门科技技能之一。从 2016 年到 2026 年，网络开发人员的就业率预计将增长 15%，远高于所有职业的平均水平。这是提高你的技能和开始你的网络开发职业生涯的好时机。在本文中，我们将按以下顺序讨论一些 Web 开发项目，它们将帮助您自己构建应用程序:

*   网络开发职业
*   Web 开发项目

1.  响应式布局
2.  动态网页
3.  答问比赛

# 网络开发职业

web 开发人员是专门使用客户机-服务器模型开发万维网应用程序的程序员。他们还负责设计，编码和修改网站，从布局到功能，并根据客户的规格。

![](img/8d42425c34eb6defa815520009c7504b.png)

你可以找到受过网络开发培训的专业人士，他们是计算机程序员、软件工程师，甚至是专注于网络的图形设计师。一些关键的工作角色是:

*   Web 开发人员使用编程和技术技能来构建网站的外观和用户体验。平均工资在卢比左右。480,694.
*   **计算机程序员**——计算机程序员通过编写和测试代码来开发和调整软件的正确功能。平均工资范围在 23.2 万卢比至 100 万卢比之间。
*   **网页设计师**——网页设计师在网站的前端工作，关心外观和用户体验。印度网页设计师的平均工资是 281，410 卢比。
*   图形网页设计师 -图形设计师致力于通过创建图形和其他视觉媒体来增强用户体验或应用程序。平均工资从 118 万卢比到 619 万卢比不等。

现在你知道了职业发展，让我们来看看一些网页开发项目，这将有助于你更好地理解网页设计的过程，也有助于你建立自己的项目。

# Web 开发项目

Web 开发项目分为三个级别- **初级、**和**高级**。我们将讨论项目的不同层次以及代码是如何工作的。这将帮助你更好地理解 web 开发的过程，并为你提供使用不同的脚本语言构建自己的网站的想法。所以，让我们从基础水平项目开始。

# 响应式布局

前端开发人员的一个主要角色是理解响应式设计原则，以及如何在编码端实现它们。

在这个项目中，我们将创建一个单一的响应页面的基本布局，以及它如何在 web 开发中建立多用途的网站。第一步是创建 HTML 布局并设计网页的头部。

```
<!DOCTYPE html>
<html>

<head>
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<style>
* {
box-sizing: border-box;
}

.menu {
float: left;
width: 20%;
text-align: center;
}

.menu a {
background-color: #deeba6;
padding: 8px;
margin-top: 7px;
display: block;
width: 100%;
color: black;
}

.main {
float: left;
width: 60%;
padding: 0 20px;
}

.right {
background-color: #f0b569;
float: left;
width: 20%;
padding: 15px;
margin-top: 7px;
text-align: center;
}

[@media](http://twitter.com/media) only screen and (max-width:620px) {

/* For mobile phones: */
.menu,
.main,
.right {
width: 100%;
}
}
</style>
</head>

<body style="font-family:Verdana;color:#0f0f0f;">

<div style="background-color:#4168bd;padding:15px;text-align:center;">
<h1>Welcome to Edureka</h1>
</div>

<div style="overflow:auto">
<div class="menu">
<a href="<a href="[https://www.edureka.co/data-science-certification-courses](https://www.edureka.co/data-science-certification-courses)">[https://www.edureka.co/data-science-certification-courses](https://www.edureka.co/data-science-certification-courses)</a>">Data Science</a>
<a href="<a href="[https://www.edureka.co/cloud-computing-certification-courses](https://www.edureka.co/cloud-computing-certification-courses)">[https://www.edureka.co/cloud-computing-certification-courses](https://www.edureka.co/cloud-computing-certification-courses)</a>">Cloud Computing</a>
<a href="<a href="[https://www.edureka.co/big-data-and-analytics](https://www.edureka.co/big-data-and-analytics)">[https://www.edureka.co/big-data-and-analytics](https://www.edureka.co/big-data-and-analytics)</a>">Big Data</a>
<a href="<a href="[https://www.edureka.co/masters-program/full-stack-developer-training](https://www.edureka.co/masters-program/full-stack-developer-training)">[https://www.edureka.co/masters-program/full-stack-developer-training](https://www.edureka.co/masters-program/full-stack-developer-training)</a>">Full Stack</a>
</div>

<div class="main">
<h2>Edureka!</h2>
<p>Your One Stop Solution to Trending Technologies.</p>
</div>

<div class="right">
<h2>About</h2>
<p>Edureka is an online technical training platform that offers Big Data, cloud computing, artificial intelligence
and blockchain</p>
</div>
</div>

<div style="background-color:#96ebf7;text-align:center;padding:10px;margin-top:7px;">© edureka.co</div>

</body>

</html>
```

在这个 web 开发项目中，我们创建了一个网页的基本响应布局，它由 edureka 网站的不同课程链接组成。

**输出:**

![](img/d167a65656e69b6cbb4f00ec4c224fc5.png)

现在，这一切都是关于创建一个基本的响应布局页面。在下一个网页开发项目中，我们将借助响应式设计构建一个动态网页。

# 动态网页

在本例中，我们将创建一个动态网页，展示环境意识，并在单个布局页面中添加活动的不同部分。

第一步是在单个布局页面中创建具有不同层或分区的 HTML 布局。

dynamic.html

```
<!doctype html>

<html>

<head>

<meta name="viewport" content="width=device-width, initial-scale=1"><!-- Critical to responsive design-->
<meta charset="utf-8">
<title>Responsive Layout</title>

</head>
<link rel="stylesheet" href="layout.css">

<body>

<header>

<nav>
<ul>
<li><a href=" ">Home</a></li>
<li><a href=" ">About</a></li>
<li><a href=" ">Contact</a></li>
</ul>
</nav>
</header>

<section class="home-hero">
<!--container class used to ensure contents do not touch edge of box see css-->
<div class="container">
<h1 class="title">Save our EARTH!
</h1>

<a href="" class="button button-accent">See our Work</a>
</div>
</section>

<div class="container">
<section class="home-about">

<div class="home-about-textbox">
<h1>What have we done?</h1>
<p>Climate Change is Real! We have done enough damage to the environment. It's time to pay for our sins. Let's join hands and make this a better place for living and hand over a cleaner environment to our next generation. </p>

</div>

</section>
</div>

<section class="portfolio">
<h1> Bring The Change- Be The Change!</h1>

<!-- portfolio item #1 -->
<figure class="port-item">
<img src="environment.png" alt="portfolio item">
<figcaption class="port-description">
<p>One Plant-One Life</p>
<a href="" class="button button-accent button-small">See project details</a>
</figcaption>
</figure>

<!-- portfolio item #2 -->
<figure class="port-item">
<img src="Plastic.png" alt="portfolio item">
<figcaption class="port-description">
<p>Don't Litter!</p>
<a href="" class="button button-accent button-small">See project details</a>
</figcaption>
</figure>

<!-- portfolio item #3 -->
<figure class="port-item">
<img src="factory.png" alt="portfolio item">
<figcaption class="port-description">
<p>Stop Factory Pollution</p>
<a href="" class="button button-accent button-small">See project details</a>
</figcaption>
</figure>

<!-- portfolio item #4 -->
<figure class="port-item">
<img src="glacier.png" alt="portfolio item">
<figcaption class="port-description">
<p>No more Melting Glaciers</p>
<a href="" class="button button-accent button-small">See project details</a>
</figcaption>
</figure>

<!-- portfolio item #5 -->
<figure class="port-item">
<img src="turtle.png" alt="portfolio item">
<figcaption class="port-description">
<p>Stop Single Use Plastic</p>
<a href="" class="button button-accent button-small">See project details</a>
</figcaption>
</figure>

<!-- portfolio item #6 -->
<figure class="port-item">
<img src="forestfire.png" alt="portfolio item">
<figcaption class="port-description">
<p>Don't Let it Burn!</p>
<a href="" class="button button-accent button-small">See project details</a>
</figcaption>
</figure>

</section>

<section class="cta">
<div class="container">
<h1 class="title title-cta">Make your Contribution!
<span>Let's work together for a better future!</span>
</h1>

<a href="" class="button button-dark">Get Started</a>
</div>
</section>

<footer>
<div class="container">
<div class="col-3"> <!--footer paragraph-->

<p>
This is an initative to spread awareness about climate change. We must join hands and take steps towars saving the Earth!
</p>

</div>

<!-- Footer links 2 -->
<div class="col-1">
<ul class="unstyled-list">
<li><strong>Events</strong></li>
<li>Registration </li>
<li>Rallies</li>
<li>Plantation drive </li>
<li>Clean up drive </li>
</ul>
</div>

</div>

</footer>

</body>

</html>
```

**一旦你创建了 HTML 布局，就该在 CSS 的帮助下设计网页样式，让它看起来更有趣。**

****dynamic.css****

```
* {
box-sizing: border-box; /* this model does not add the padding measurements to the total size of your box*/
transition: all ease-in-out 250ms;
}

body {
margin: 0;
font-family: 'Raleway', sans-serif;
text-align: center;
}

img {
max-width: 100%;
height: auto;
}

.container{
width: 95%;
max-width: 70em;
margin: 0 auto;
}

.clearfix::after,
section::after,
footer::after{
content:'';
display: block;
clear: both;

}

/* Column system (footer items)
==============================================*/

[class^=col-] /*Any class which starts with the string "col" recieves the following properties*/
{
width: 100%;
margin-top: 1em;
}
[class^=col-]:first-child
{
margin-top: 0;
}

.col-1{
width: 33.3333334%;
float: left;
}

[@media](http://twitter.com/media) (min-width:40rem){
[class^=col-] /*Any class which starts with the string "col" recieves the following properties*/
{ float: left;
padding: 0 .5em;
margin-top: 0;
}

[class^=col-]:first-child
{
padding-left: 0;

}

[class^=col-]:last-child
{
padding-right: 0;
}
.col-3{
width: 50%;
}

.col-1{
width:16.666666%
}
}
/* Typography
rem is calculated based on the default h1 value of 16px
for example a media query trigered at 60rem responds to viewports wider than 960px
16*60= 960
==============================================*/
h1{
font-weight: 300;
font-size: 1.8rem;
margin-top: 0;
}

p{
margin-top:0;
line-height: 1.4;
}

p:last-of-type{
margin-bottom: 0;
}

[@media](http://twitter.com/media) (min-width:60rem){

p{
font-size: 1.2rem;
line-height: 1.6;
}
}

.title{
font-size: 2.5em;
margin-botom: 1.5em;
font-weight: 900;
margin-top: 1em;
}
.title span {
font-weight: 300;
display: block;
font-size: .9em;
}

.title-cta{
margin: 0 0 .5em;
}
.unstyled-list{
margin: 0;
padding: 0;
list-style: none;

}

[@media](http://twitter.com/media) (min-width:60rem) {

p {
font-size: 1.2rem;
line-height: 1.6;
}

.title {
font-size: 3.7rem;
}
}

/*==Buttons==*/

.button{
display: inline-block;
font-size: 1.15rem;
text-decoration: none;
text-transform: uppercase;
border-width: 2px;
border-style: solid;
padding: .5em 1.75em;
}

[@media](http://twitter.com/media)(min-width: 60rem){

.button{
font-size: 1.5em;
}
}

.button-small{
font-size: .7rem;
font-weight: 700;
}

.button-accent{
color: #f3f5f4;
border-color: #f3f5f4;
}

.button-accent:hover,
.button-accent:focus {
background: #00ff6c;
color: #232323;
}

.button-dark{
color: #232323;
border-color: #232323;
}

.button-dark:hover,
.button-dark:focus {
background: #232323;
color: #00ff6c;
}

/* HEADER
==============================================*/

header {
position:absolute;
left: 0;
right: 0;
margin: 1em;

}

nav ul {
margin: 0;
padding: 0;
list-style: none;
}

nav li {
display: inline-block;
margin: 1em;

}

nav a {

font-weight: 900;
text-decoration: none;
padding: .5em;
text-transform: uppercase;
color: rgb(24, 24, 26);
font-size: .8rem;

}

nav a:hover ,
nav a:focus {
color: rgb(2, 31, 4);
}

[@media](http://twitter.com/media) (min-width: 60rem) {

.logo{
float: left;
}

nav{
float: right;
}
}

/* hero
==============================================*/

.home-hero {
background: url(earth.jpg);
padding: 10em 0;
color: rgb(19, 18, 18);
background-size: cover;
background-position: center;

}

[@media](http://twitter.com/media)(min-width: 60rem){

.home-hero{
height: 100vh;
padding-top: 35vh;
}
}

/* home-hero-about-textbox
==============================================*/

.home-about-textbox{
background-color: #4b4a4a;
padding: 4em;
width: 100vw;
margin-left: 20%;
outline: 2px solid #00ff6c;
outline-offset: -2.5em;
color: #fff;
position: relative;
}

.home-about-textbox h1 {
color: #00ff6c;
position:absolute;
left: 50%;
transform: translateX(-50%);
top: .75em;
background: #232323;
padding: 0 .145em;
}

[@media](http://twitter.com/media)(min-width: 25rem){

h1{
font-size: 2rem;
}
.home-about-textbox h1 {
top: .6em;
padding: 0 .25em;
}
}

[@media](http://twitter.com/media)(min-width: 40rem){

h1{
font-size: 2.5rem;
}

.home-about{
background-image: url(earth1.jpg);
background-repeat: no-repeat;
padding-bottom: 20em;

}

.home-about-textbox{
width: 50%;
padding: 5em;
outline-offset: -3.75em;
margin-right: -2.5%;
bottom: -25em;
text-align: left;
box-shadow: 0 0 4em rgba(0, 0, 0, .3);

}

.home-about-textbox h1 {
top: .75em;
left: 6rem;
transform: translateX(0);
}
}

/* Portfolio items
==============================================*/

.portfolio {
margin: 7em 0 0;
}

.port-item{
margin: 0;
position: relative;

}

.port-item img{
display:block;
}

.port-description {
position: absolute;
z-index: 100;
bottom: 0em;
left: 0em;
right: 0em;
color: white;
background: rgba(0,0,0,.6);
padding-bottom: 2em;

}

.port-description p {
margin: 1em;
}

[@media](http://twitter.com/media) (min-width: 37rem) {
.port-item{
width: 50%;
float: left;
overflow: hidden;
}
}

[@media](http://twitter.com/media) (min-width: 60rem) {
.port-item img{
width: 100%; /*added this to solve a bug on 1920 * 1080 monitors
remove and view to see bug */
}

.port-item{
width: 33.3333333333333334%;
height: auto;
float: left;

}
.port-description {
transform: translateY(150%);
}

.port-item:hover .port-description{
transform: translateY(0%);
}
.port-item:focus-within .port-description {
transform: translateY(0%);}
}

/* CTA SECTION
==============================================*/
.cta {
background-color: #53e4c4;
padding: 5em 0;
}

/* Footer
==============================================*/
footer {
background: #232323;
color: #fff;
text-align: left;
padding: 5em 0;
}
```

****输出:****

**![](img/ff94b8765ed89ed68b2ad2b17e62f9c1.png)****![](img/82afd6ca3fe0d3b0c781dc556bb46af1.png)****![](img/ef1dc65b370e5c1b709ce28588d04749.png)**

**现在，我们将进入 web 开发项目的最后阶段，在 HTML、CSS 和 JavaScript 的帮助下创建一个有趣的问答游戏。**

# **答问比赛**

**在这个项目中，我们将创建一个带有多项选择答案的问答游戏，并根据正确答案的数量得出最终分数。**

**要设置 JavaScript 测验的结构，我们需要从下面的 HTML 代码开始:**

****quiz.html****

```
<html>
    <head>
        <title>Quiz Game</title>
    </head>
        <link rel="stylesheet" href="quiz.css">
        <h1>Do You Know?</h1>
        <div class="quiz-container">
          <div id="quiz"></div>
        </div>
        <button id="previous">Previous Question</button>
        <button id="next">Next Question</button>
        <button id="submit">Submit Quiz</button>
        <div id="results"></div>
<script type="text/javascript", src='quiz.js'></script>
</html>
```

**接下来，我们需要一种方法来建立一个测验，显示结果，并把它们放在一起。我们可以在 JavaScript 的帮助下设计我们的功能。**

****quick . js****

```
(function() {
const myQuestions = [
{
question: "Which sea creature has three hearts? ",
answers: {
a: "Octopus",
b: "Blue Whale",
c: "Sea Turtle"
},
correctAnswer: "a"
},
{
question: "What is the Italian word for pie?",
answers: {
a: "Donut",
b: "Pie cake",
c: "Pizza"
},
correctAnswer: "c"
},
{
question: "Which is the only mammal that can not jump?",
answers: {
a: "Snake",
b: "Elephant",
c: "Kangaroo",
},
correctAnswer: "b"
}
];

function buildQuiz() {
// we'll need a place to store the HTML output
const output = [];

// for each question...
myQuestions.forEach((currentQuestion, questionNumber) => {
// we'll want to store the list of answer choices
const answers = [];

// and for each available answer...
for (letter in currentQuestion.answers) {
// ...add an HTML radio button
answers.push(
`<label id="${questionNumber}${letter}" href="#">
<input type="radio" name="question${questionNumber}" value="${letter}"} id="${questionNumber}${letter}"">
${letter} :
${currentQuestion.answers[letter]}
</label>`
);
}

// add this question and its answers to the output

output.push(
`<div class="slide">
<div class="question"> ${currentQuestion.question} </div>
<div class="answers"> ${answers.join("")} </div>
</div>`
);
});

// finally combine our output list into one string of HTML and put it on the page

quizContainer.innerHTML = output.join("");
}

function showResults() {
// gather answer containers from our quiz

const answerContainers = quizContainer.querySelectorAll(".answers");

// keep track of user's answers

let numCorrect = 0;

// for each question...

myQuestions.forEach((currentQuestion, questionNumber) => {

// find selected answer

const answerContainer = answerContainers[questionNumber];
const selector = `label input[name=question${questionNumber}]:checked`;
const userAnswer = (answerContainer.querySelector(selector) || {}).value;
const answerID = (answerContainer.querySelector(selector) || {}).id;
const selector1 = `label[id="${answerID}"]`; //Select user's answer
var answerElem = answerContainer.querySelector(selector1);
const selector2 = `label[id="${questionNumber}${currentQuestion.correctAnswer}"]`;
var answerElem1 = answerContainer.querySelector(selector2);

// if answer is correct

if (userAnswer === currentQuestion.correctAnswer) {
// add to the number of correct answers

numCorrect++;

// color the answers green

//console.log(answerElem)

answerElem.style.background = "#70F85A";
answerElem.style.fontWeight = "900";

} else {
// if answer is wrong or blank
// color the answers red

answerElem1.style.color="#70F85A";
answerElem.style.background="#FD2929";
answerElem1.style.fontWeight = "900";
//console.log(answerContainers)
}
});

// show number of correct answers out of total

resultsContainer.innerHTML = `${numCorrect} out of ${myQuestions.length}`;
}

function showSlide(n) {
slides[currentSlide].classList.remove("active-slide");
slides[n].classList.add("active-slide");
currentSlide = n;

if (currentSlide === 0) {
previousButton.style.display = "none";
} else {
previousButton.style.display = "inline-block";
}

if (currentSlide === slides.length - 1) {
nextButton.style.display = "none";
submitButton.style.display = "inline-block";
} else {
nextButton.style.display = "inline-block";
submitButton.style.display = "none";
}
}

function showNextSlide() {
showSlide(currentSlide + 1);
}

function showPreviousSlide() {
showSlide(currentSlide - 1);
}

const quizContainer = document.getElementById("quiz");
const resultsContainer = document.getElementById("results");
const submitButton = document.getElementById("submit");

// display quiz right away

buildQuiz();

const previousButton = document.getElementById("previous");
const nextButton = document.getElementById("next");
const slides = document.querySelectorAll(".slide");
let currentSlide = 0;

showSlide(0);

// on submit, show results

submitButton.addEventListener("click", showResults);
previousButton.addEventListener("click", showPreviousSlide);
nextButton.addEventListener("click", showNextSlide);
})();
```

**最后，我们可以使用 CSS 为这个游戏添加不同的风格。**

****quiz.css****

```
[@import](http://twitter.com/import) url(<a href="[https://fonts.googleapis.com/css?family=Work](https://fonts.googleapis.com/css?family=Work)">[https://fonts.googleapis.com/css?family=Work](https://fonts.googleapis.com/css?family=Work)</a>+Sans:300,600);

body{
font-size: 30px;
font-family: 'Work Sans', sans-serif;
color: rgb(24, 23, 23);
font-weight: 300;
text-align: center;
background-color: #f8e8f2;
}
h1{
font-weight: 300;
margin: 0px;
padding: 10px;
font-size: 40px;
background-color: rgb(9, 107, 102);
color: #fff;
}
.question{
font-size: 40px;
margin-bottom: 10px;
}
.answers {
margin-bottom: 20px;
text-align: left;
display: inline-block;
}
.answers label{
display: block;
margin-bottom: 10px;
}
button{
font-family: 'Work Sans', sans-serif;
font-size: 22px;
background-color: rgb(218, 167, 57);
color: #fff;
border: 0px;
border-radius: 3px;
padding: 20px;
cursor: pointer;
margin-bottom: 20px;
}
button:hover{
background-color: #38a;
}

.slide{
position: absolute;
left: 0px;
top: 0px;
width: 100%;
z-index: 1;
opacity: 0;
transition: opacity 0.5s;
}
.active-slide{
opacity: 1;
z-index: 2;
}
.quiz-container{
position: relative;
height: 200px;
margin-top: 40px;
}
```

****输出:****

**![](img/ee666a7606a9b919db68888c801ec306.png)****![](img/8ef402141b5007863694b46605fe6e2f.png)****![](img/38da1ad627a7ea58e97ed33fea77eb35.png)**

**这些是一些网络开发项目。就这样，我们来到了这篇文章的结尾。我希望你理解了项目的不同层次，并且明白了如何建立自己的网页并根据你的需要来设计它们。**

**如果你想查看更多关于人工智能、Python、道德黑客等市场最热门技术的文章，那么你可以参考 [Edureka 的官方网站。](https://www.edureka.co/blog/?utm_source=medium&utm_medium=content-link&utm_campaign=web-development-projects)**

**请留意本系列中的其他文章，它们将解释 Web 开发的各个方面。**

> **1.[前端开发者技能](/edureka/front-end-developer-skills-ebb32d19f488)**
> 
> **2.[前端开发者简历](/edureka/front-end-developer-resume-c3d443f98296)**
> 
> **3. [HTML vs HTML5](/edureka/html-vs-html5-83302f95652e)**

***原载于 2019 年 10 月 18 日*[*https://www.edureka.co*](https://www.edureka.co/blog/web-development-projects/)*。***