# 从JavaScript红宝书中解读js面试题

## 你了解JavaScript的历史吗？给我讲解一下

上个世纪Web发展日益流行，对于客户端脚本语言的需求越来越强烈, 网景公司的Brendan Eich开发了叫做LiveScript，后来网景公司与Sun公司结盟，为了炒作，改名为JavaScript。后来微软发布了JScript其中包含自己的JavaScript实现，这就意味这JavaScript有两个版本一个是IE的一个是Netscape Navigator的，这就使得JavaScript形成了标准化，JavaScript1.1作为提案被提交给欧洲计算机制造协会(Ecma)。TC39承担了标准化的任务，他们打造出了ECMA-262, 也就是ECMAScript。

## 简单介绍一下JavaScript的实现吧

完整的JavaScript实现包含:

1. ECMAScript: 定义了实现ECMA-262规范的所有方面, 无关平台，定义这个语言的核心功能
2. DOM: 提供与网页内容交互的方法
3. BOM: 提供与浏览器交互的方法

## 介绍一下\<script>

JavaScript插入HTML就是要用\<script>

有这几种属性:

1. async: 代表着立即开始异步下载脚本、但不能阻止其他页面动作, 只对外部脚本文件有效
2. charset: 使用src属性指定的代码字符集
3. crossorigin: 配置相关请求的CORS设置。默认不使用CORS。crossorigin="anoymous"配置文件请求不必设置凭据，crossorigin="use-credentials"设置凭据, 代表出站请求会包含凭据
4. defer: 标识脚本可以延迟到文档完全被解析和显示之后再执行。只对于脚本文件有效
5. integrity: 允许比对接收到的资源和指定的加密签名以验证子资源的完整性。如果接收到的签名和该属性指定的签名不匹配，页面报错，脚本不执行。可以确保CDN不会提供恶意内容
6. language: 废弃
7. src: 要执行的代码的外部文件
8. type: 代替language，标识代码块中的脚本语言的内容类型(MIME类型)。它的值始终都是"text/javascript", 如果是module，当作ES6模块

## 什么是\<noscript>你知道吗?

\<noscript>实际上使用来解决不支持JavaScript浏览器提供替代的内容。这个元素可以出现在任何可以出现的\<body>中的HTML元素, \<script>除外。

有两种情况，浏览器会显示包含在\<noscript>中的内容:

1. 浏览器不支持脚本
2. 浏览器对脚本的支持被关闭

## 介绍一下严格模式

严格模式是ES5增加的概念。
严格模式是一种不同的JavaScript解析和执行模型
"use script";
它是一个预处理指令，任何一个JavaScript引擎都会切换到严格模式，目的是不破坏ES3语法

## var, let, const有什么区别与联系?

首先，这三个关键字用于声明变量, var任何版本都可以用，而const和let在ES6之后才可以用

先说说var的声明提升吧
```JavaScript
function foo() {
  console.log(age);
  var age = 18;
}
foo(); // undefined
// 不会报错，ECMAScript会等价
function foo1() {
  var age;
  console.log(age);
  age = 18;
}
foo();
/**
 * 这就是变量提升(hoist), 将变量声明拉到函数作用域的顶部。
 */
```

但是var的问题是var声明的函数作用域，let和const声明的是块作用域

```js
if (true) {
  var name = 'cc';
  console.log(name);
}
console.log(name);
if (true) {
  let age = 26;
  console.log(age);
}
console.log(age); // 报错 ReferenceError: age没有定义
```

块作用域是函数作用域的子集，因此适用于var的作用域限制同样也适用于let。
let还不允许同一个块作用域出现冗余的声明。

**暂时性死区**
let声明的变量不会在作用域中被提升

```js
console.log(name); // undefined
var name = 'CC';

console.log(age); // 报错age未定义
let age = 18;
```

**全局声明**
使用let在全局作用域中声明的变量不会成为window对象的属性(var会)

```js
var name = 'cc';
console.log(window.name); // cc
let age = 18;
console.log(window.age); // undefined
```

不过，let声明仍然是在全局作用域中发生的，相应变量会在页面的生命周期内存续。因此，为了避免SyntaxError，必须确保页面不会重复声明同一个变

**条件声明**

使用var声明变量时，由于声明会被提升，JavaScript引擎会自动将多余的声明在作用域顶部合并为一个声明。

**for循环中的let声明**

let出现之前，for循环定义的迭代变量会渗透到循环体内部

```js
for (var i = 0; i < 5; i++) {
  // 循环逻辑
}
console.log(i); // 5

for (var j = 0; j < 5; j++) {
  setTimeout(() => console.log(i), 0); 
}
// 5 5 5 5 5
// 是因为在退出循环时，迭代变量保存的是导致循环退出的值：5
```

使用let就可以解决这个问题，let声明迭代变量时，JavaScript引擎会在后台为每个迭代循环声明一个新的迭代变量。每个setTimeout引用的都是不同的变量实例

```js
for (let i = 0; i < 5; i++) {
  setTimeout(() => console.log(i), 0);
}
```

const的行为基本与let一样唯一的区别是const声明的是常量且const声明变量的同时必须要初始化变量。
不过const声明的限制只适用于它指向的变量的引用。如果const引用的是对象，那么修改对象内部属性其实不违反const的限制
JavaScript引擎也会为for循环中的let声明分别创建独立的变量实例
