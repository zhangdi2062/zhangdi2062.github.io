---
title: js知识
tags:
  - js
categories:
  - 前端
date: 2024-03-01 21:00:17
---

## 概念解释

### JavaScript

一门解释型语言，单线程，面向对象

### ECMAScript

标准化组织 ECMA，规定了浏览器脚本语言的标准 ECMAScript，是 JavaScript 的规格标准，JavaScript 根据它实现

### ES6

ECMAScript2015（以下简称 ES6），在 2015 年 6 月正式发布，相较于 5.0 版本，更新了很多内容
标准委员会决定标准在每年 6 月发布，用年号区分，如 ES2016、ES2017
常说的 ES6 泛指 JavaScript 语言的下一代标准

### V8 引擎

Google 开发的 JavaScript 解释器，用于 Chrome 浏览器，也用于 Node.js，用来执行 javascript 代码

## async 和 defer 和 type=module

```html
<head>
  <script src="url"></script>
</head>
```

下载和解析 js 文件会阻塞 html 解析

```html
<head>
  <script src="url" async></script>
</head>
```

下载不会阻塞 html 解析，下载完成后会立马解析 js 文件，阻塞 html 渲染
适用于分析、广告、或跟踪的脚本

```html
<head>
  <script src="url" defer></script>
  <script src="url" type="module"></script>
</head>
```

下载和解析 js 文件都不会阻塞 html 解析，等待页面渲染完成后再解析 js
适用于初始化页面内容或绑定事件处理函数的脚本

相关事件：`DOMContentLoaded` 和 `load`

DOMContentLoaded 在第一和第三种情况下运行完成后会触发，异步加载不会触发

load 事件在整个文档加载完成后触发

## 类型转换规则

### 原始 -> 数字

true: 1
false: 0
null: 0
undefined: NaN
string: 空字符串: 0
去掉引号，不是数字就是 NaN
Number(' \n\r\t '): 0

### 所有 -> bool

null: false
undefined: false
number: 0: false
NaN: false
其他(包括无穷): true
对象: true

### 原始 -> 字符串

null: 'null'
undefined: 'undefined'
number: '数字'
boolean: true: 'true'
false: 'false'
对象: object: '[object Object]'
array: []: ''
[{}, undefined,10, 'abc', null, true]: '[object Object],,10,abc,,true'

### 对象 -> 原始

对象调用`valueOf`，如果返回值为对象，继续调用`toString`，得到的还是对象，报错

## for in 和 for of

for in

1. 遍历对象的可枚举属性，包括对象自身的属性以及继承的属性
2. 不保证遍历顺序

for of

1. 遍历可迭代对应的属性值
2. 迭代顺序与索引顺序一致
3. 对象不能迭代

```javascript
for await (variable of iterable) {
  statement;
}
```

引申知识：

判断是否是可迭代对象：`data != null && typeof data[Symbol.iterator] === 'function'`

## requestAnimationFrame

出现原因：解决定时器刷新频率不稳定问题

应用场景：动画

具体实现：

```js
// requestAnimationFrame和setTimeout类似，调用是一次性的
// 接收一个函数，该回调函数会在浏览器下一次重绘之前执行。回调函数执行次数通常与浏览器屏幕刷新次数相匹配
// 当页签在后台运行时，requestAnimationFrame会被停止调用，用来提升性能和电池寿命

let timer = null;
let i = 0;
function fn() {
  if (i < 100) {
    i++;
    timer = requestAnimationFrame(fn);
  } else {
    cancelAnimationFrame(timer);
  }
}
fn();
```

## 箭头函数的特点

函数：流程的封装

箭头函数：简单纯粹的函数，消除函数的二义性

函数的二义性：普通函数通过 function 构造，一方面可以直接调用，另一方面可以实例化，给调用者如何调用造成了困难，基本上通过开发常识来区分，比如函数名大写的构造函数

箭头函数和普通的函数的区别：

1. 没有`this` `super` `arguments`，因为没有 this，故无法通过任何手段绑定`this`（有 call、apply 方法，但无法修改 this 绑定）
2. 不能使用`new`调用
3. 没有原型

## call、apply

用途：

1. 改变 this 指向

2. 实现继承

```javascript
function Animal(name) {
  this.name = name;
  this.showName = function () {
    console.log(this.name);
  };
}
function Cat(name) {
  Animal.call(this, name);
}
var cat = new Cat("Black Cat");
cat.showName(); //Black Cat
```

3. 为类属组(arguments/nodeList)添加 push、pop 方法

```javascript
(function () {
  Array.prototype.push.call(arguments, "王五");
  console.log(arguments); //['张三','李四','王五']
})("张三", "李四");
```

4. 合并数组

```javascript
let arr1 = [1, 2, 3];
let arr2 = [4, 5, 6];
Array.prototype.push.apply(arr1, arr2); //将arr2合并到了arr1中
// 注：使用apply和call的结果不一样
```

5. 求数组最大值

```javascript
Math.max(null, arr);
```

6. 判断字符类型

```javascript
Object.prototype.toString.call({});
```

## 属性描述符

```js
const obj = {
  property1: 42,
};

const descriptors1 = Object.getOwnPropertyDescriptors(obj);

const property = descriptors1.property1;
property.value; // 属性值 42
property.writable; // 是否可修改 true
property.enumerable; // 是否可枚举 true
property.configurable; // 属性描述符是否可再次修改
```

## cookie

### SameSite

`SameSite`的作用：限制**跨站**请求，决定是否发送即将请求网站的 cookie，防止攻击者利用用户的登录状态来执行未经授权的操作。

可选值：

`none`：不做任何限制 需配合`https`使用，可能会有 CSRF 攻击
使用方式：`Set-Cookie: ..;Secure;SameSite=None`

`Lax`：默认值，阻止发送 Cookie，但对超链接`<a href=""></a>`放行

`Strict`：阻止发送 Cookie

### Domain 站点

使用：`Set-Cookie: ..;domain=www.a.com`

> 判断是否跨站：站点名一致为同站，一般为后面的域名如 `a.foo.com` & `b.c.foo.com`。\n 但要排除公共后缀，如`a.github.io` & `b.github.io`为跨站，因为`github.io`为公共后缀，a 和 b 不同站。公共后缀的查询网址：https://publicsuffix.org/list/public_suffix_list.dat

## Promise

### 含义

Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上说，Promise 是一个对象，从它可以获取异步操作的消息。

### 特点

1. 对象的状态不受外界影响，只有异步操作的结果，能改变他的状态
2. 状态一旦改变，就不会再变，任何时候都可以得到这个结果

### 缺点

1. 无法取消
2. 如果没有设置回调函数，则内部抛出的错误无法反应到外部
3. 当处于 pending 状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）

### 基本用法

一般来说，调用 resolve 或 reject 以后，Promise 的使命就完成了，后继操作应该放到 then 方法里面，而不应该直接写在 resolve 或 reject 的后面。所以，最好在它们前面加上 return 语句，这样就不会有意外。

### Promise.prototype.then()

then 方法返回的是一个新的 Promise 实例（注意，不是原来那个 Promise 实例）。因此可以采用链式写法，即 then 方法后面再调用另一个 then 方法。

### Promise.prototype.catch()

```js
// bad
promise.then(
  function (data) {
    // success
  },
  function (err) {
    // error
  }
);

// good
promise
  .then(function (data) {
    //cb
    // success
  })
  .catch(function (err) {
    // error
  });
```

上面代码中，第二种写法要好于第一种写法，理由是第二种写法可以捕获前面 then 方法执行中的错误，也更接近同步的写法（try/catch）。因此，建议总是使用 catch()方法，而不使用 then()方法的第二个参数。

### Promise.prototype.finally()

finally()方法用于指定不管 Promise 对象最后状态如何，都会执行的操作，并且回调函数不接受任何参数，finally 方法总是会返回原来的值

```javascript
// resolve 的值是 undefined
Promise.resolve(2).then(
  () => {},
  () => {}
);

// resolve 的值是 2
Promise.resolve(2).finally(() => {});
```

### Promise.all()

Promise.all()方法接受一个数组作为参数，p1、p2、p3 都是 Promise 实例，如果不是，就会先调用下面讲到的 Promise.resolve 方法，将参数转为 Promise 实例，再进一步处理。另外，Promise.all()方法的参数可以不是数组，但必须具有 Iterator 接口，且返回的每个成员都是 Promise 实例。

p 的状态由 p1、p2、p3 决定，分成两种情况。

（1）只有 p1、p2、p3 的状态都变成 fulfilled，p 的状态才会变成 fulfilled，此时 p1、p2、p3 的返回值组成一个数组，传递给 p 的回调函数。

（2）只要 p1、p2、p3 之中有一个被 rejected，p 的状态就变成 rejected，此时第一个被 reject 的实例的返回值，会传递给 p 的回调函数。

注意，如果作为参数的 Promise 实例，自己定义了 catch 方法，那么它一旦被 rejected，并不会触发 Promise.all()的 catch 方法。

### Promise.race()

只要 p1、p2、p3 之中有一个实例率先改变状态，p 的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给 p 的回调函数。

### Promise.allSettled()

用来确定一组异步操作是否都结束了（不管成功或失败），一旦发生状态变更，状态总是 fulfilled

返回结果为数组，格式如下

```javascript
// 异步操作成功时
{status: 'fulfilled', value: value}

// 异步操作失败时
{status: 'rejected', reason: reason}
```

### Promise.any()

只要参数实例有一个变成 fulfilled 状态，包装实例就会变成 fulfilled 状态；

如果所有参数实例都变成 rejected 状态，包装实例就会变成 rejected 状态。

Promise.any()抛出的错误是一个 AggregateError 实例，这个 AggregateError 实例对象的 errors 属性是一个数组，包含了所有成员的错误。

### Promise.resolve()

将现有对象转为 Promise 对象

（1）参数是一个 Promise 实例

如果参数是 Promise 实例，那么 Promise.resolve 将不做任何修改、原封不动地返回这个实例。

（2）参数是一个 thenable 对象

thenable 对象指的是具有 then 方法的对象，比如下面这个对象。

let thenable = {
then: function(resolve, reject) {
resolve(42);
}
};
Promise.resolve()方法会将这个对象转为 Promise 对象，然后就立即执行 thenable 对象的 then()方法。

let thenable = {
then: function(resolve, reject) {
resolve(42);
}
};

let p1 = Promise.resolve(thenable);
p1.then(function (value) {
console.log(value); // 42
});
上面代码中，thenable 对象的 then()方法执行后，对象 p1 的状态就变为 resolved，从而立即执行最后那个 then()方法指定的回调函数，输出 42。

（3）参数不是具有 then()方法的对象，或根本就不是对象

如果参数是一个原始值，或者是一个不具有 then()方法的对象，则 Promise.resolve()方法返回一个新的 Promise 对象，状态为 resolved。

const p = Promise.resolve('Hello');

p.then(function (s) {
console.log(s)
});
// Hello
上面代码生成一个新的 Promise 对象的实例 p。由于字符串 Hello 不属于异步操作（判断方法是字符串对象不具有 then 方法），返回 Promise 实例的状态从一生成就是 resolved，所以回调函数会立即执行。Promise.resolve()方法的参数，会同时传给回调函数。

（4）不带有任何参数

Promise.resolve()方法允许调用时不带参数，直接返回一个 resolved 状态的 Promise 对象。

### Promise.reject()

Promise.reject(reason)方法也会返回一个新的 Promise 实例，该实例的状态为 rejected。

### Promise.try()

不知道或者不想区分，函数 f 是同步函数还是异步操作，但是想用 Promise 来处理它。因为这样就可以不管 f 是否包含异步操作，都用 then 方法指定下一步流程，用 catch 方法处理 f 抛出的错误。一般就会采用下面的写法。

```javascript
const f = () => console.log("now");
(() => new Promise((resolve) => resolve(f())))();
console.log("next");
```

提案

```javascript
Promise.try(() => database.users.get({id: userId}))
  .then(...)
  .catch(...)
```

## var let const

表格
| | var | let | const |
| --- | --- | --- | --- |
| 作用域 | 函数作用域、全局作用域 | 块级作用域 | 块级作用域 |
| 变量提升 | 是 | 否 | 否 |
| 重复声明 | 是 | 否 | 否 |
| 暂时性死区 | 否 | 是 | 是 |
| 跨 script 标签使用 | 是 | 是 | 是 |
| 赋值 | 是 | 是 | 否 |

### 为什么用 var 定义的变量，可以使用 window 读取，而 let 和 const 不行

var 在全局作用域下声明的变量，会成为 window 对象的属性，而 let 和 const 声明的变量不会

## 变量的解构赋值

多层解构

```javascript
let obj = {
  p: ["Hello", { y: "World" }],
};

let {
  p,
  p: [x, { y }],
} = obj;
x; // "Hello"
y; // "World"
p; // ["Hello", {y: "World"}]
```

嵌套赋值

```javascript
let obj = {};
let arr = [];

({ foo: obj.prop, bar: arr[0] } = { foo: 123, bar: true });

obj; // {prop:123}
arr; // [true]
```

已经声明的变量赋值

```javascript
let x;
({ x } = { x: 1 });
```

## 字符串的扩展

### 可遍历

字符串可使用`for...of`遍历，因为有`Iterator`遍历器接口

### 可嵌套

字符串模板可嵌套

```javascript
`My ${`name is ${name}`}`;
```

### 标签模板

```javascript
alert`123`; // 等同于 alert(123)

let a = 5;
let b = 10;
// 如果末尾是变量的话，会加入一个空字符串，否则不会
tag`Hello ${a + b} world ${a * b}`;
// 等同于
tag(["Hello ", " world ", ""], 15, 50);
```

#### 标签模板应用

1. 过滤 HTML 字符串，防止用户输入恶意内容

```javascript
let sender = '<script>alert("abc")</script>'; // 恶意代码
let message = SaferHTML`<p>${sender} has sent you a message.</p>`;
function SaferHTML(templateData) {
  let s = templateData[0];
  for (let i = 1; i < arguments.length; i++) {
    let arg = String(arguments[i]);

    // Escape special characters in the substitution.
    s += arg.replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;");

    // Don't escape special characters in the template.
    s += templateData[i];
  }
  return s;
}
// message:
// <p>&lt;script&gt;alert("abc")&lt;/script&gt; has sent you a message.</p>
```

2. 国际化处理

```javascript
i18n`Welcome to ${siteName}, you are visitor number ${visitorNumber}!`;
// "欢迎访问xxx，您是第xxxx位访问者！"
```

## 字符串的新增方法

### String.raw

String.raw()方法可以作为处理模板字符串的基本方法，它会将所有变量替换，而且对斜杠进行转义，方便下一步作为字符串来使用。

String.raw()本质上是一个正常的函数，只是专用于模板字符串的标签函数

### 实例方法：codePointAt()

### 实例方法：includes(), startsWith(), endsWith()

### 实例方法：repeat()

```javascript
// 向下取整
"na".repeat(2.9); // "nana"
// NaN 等同于 0
"na".repeat(NaN); // ""
// 负数或者 Infinity 会报错
"na".repeat(Infinity);
// RangeError
"na".repeat(-1);
// RangeError
// 参数不是数字，会先转为数字
"na".repeat("na"); // ""
```

### 实例方法：padStart()，padEnd()

头尾补全

```javascript
"abc".padStart(10, "0123456789"); // '0123456abc'
```

如果原字符串的长度，等于或大于最大长度，则字符串补全不生效，返回原字符串。

如果省略第二个参数，默认使用空格补全长度。

### 实例方法：trimStart()，trimEnd()

消除头尾空格

```javascript
const str = "abc";
str.trimStart === str.trimLeft; // true
str.trimEnd === str.trimRight; // true
```

### 实例方法：matchAll()

### 实例方法：replaceAll()

替换所有匹配的字符串，第一个参数可以是字符串或正则表达式，第二个参数可以是字符串或函数

第一个参数为正则时，必须有全局修饰符`g`，否则报错

```javascript
"aabbcc".replaceAll("b", "_");
// 'aa__cc'

// 第二个参数可以使用特殊的字符串
```

### 实例方法：at()

返回字符串给定位置的字符，可以为负数，不在范围内返回 undefined

```javascript
"abc".at(0); // "a"
```

与`abc[0]`的区别，`at`可以为负数

与`charAt`的区别，`charAt`索引超出范围时或为负数时，返回空字符串

## 正则的扩展

## 数组的扩展

### Array.from(arrayLike, mapFn, thisArg)

- 作用：将类数组对象和可遍历对象转为真正的数组

- 参数含义

| arrayLike  | mapFn                                             | thisArg                        |
| ---------- | ------------------------------------------------- | ------------------------------ |
| 类数组对象 | 可选，数组中每个元素都会执行这个函数(item, index) | 可选，执行回调函数时 this 对象 |

- 示例

```javascript
Array.from({ length: 5 }, (_, i) => i + 1); // [1, 2, 3, 4, 5]
```

## Web Worker

### 概述

Web Worker 的作用，就是为 JavaScript 创造多线程环境，允许主线程创建 Worker 线程，将一些任务分配给后者运行。等到 Worker 线程完成计算任务，再把结果返回给主线程。这样的好处是，一些计算密集型或高延迟的任务，被 Worker 线程负担了，主线程（通常负责 UI 交互）就会很流畅，不会被阻塞或拖慢。

Worker 线程一旦新建成功，就会始终运行，不会被主线程上的活动（比如用户点击按钮、提交表单）打断。这样有利于随时响应主线程的通信。但是，这也造成了 Worker 比较耗费资源，不应该过度使用，而且一旦使用完毕，就应该关闭。

### 适用场景

- 适合处理长时间运行的计算密集型任务，如复杂的算法、数据处理、文件解析等。
- 可以用来保持 UI 的流畅性，特别是在需要进行大量计算的应用中，如图像处理、游戏、数据分析等。

### 注意点

Web Worker 有以下几个使用注意点。

1. 同源限制

分配给 Worker 线程运行的脚本文件，必须与主线程的脚本文件同源。

2. DOM 限制

Worker 线程所在的全局对象，与主线程不一样，无法读取主线程所在网页的 DOM 对象，也无法使用 document、window、parent 这些对象。但是，Worker 线程可以 navigator 对象和 location 对象。

3. 通信联系

Worker 线程和主线程不在同一个上下文环境，它们不能直接通信，必须通过消息完成。

4. 脚本限制

Worker 线程不能执行 alert()方法和 confirm()方法，但可以使用 XMLHttpRequest 对象发出 AJAX 请求。

5. 文件限制

Worker 线程无法读取本地文件，即不能打开本机的文件系统（file://），它所加载的脚本，必须来自网络。

### 基本用法

#### 主线程

```javascript
// 新建 Worker 线程
const worker = new Worker("work.js");
// 向 Worker 线程发送消息
// worker.postMessage()方法的参数，就是主线程传给 Worker 的数据。它可以是各种数据类型，包括二进制数据。
worker.postMessage("Hello World");
// 接收 Worker 线程的消息
worker.onmessage = function (event) {
  console.log("Received message " + event.data);
};
// 关闭 Worker
worker.terminate();
```

#### Worker 线程

```javascript
// self 为 worker 自身
// 接收主线程的消息
self.addEventListener(
  "message",
  function (e) {
    // 向主线程发送消息
    self.postMessage("You said: " + e.data);
  },
  false
);

// 用于在 Worker 内部关闭自身
self.close();
```

## requestIdleCallback

### 工作方式

- requestIdleCallback 是在主线程空闲时执行的回调函数。浏览器会在执行高优先级任务（如用户输入、动画、渲染）后，在空闲时间执行这些回调。
- 回调函数可以接收一个 IdleDeadline 对象，提供空闲时间的截止时间和剩余时间。

### 适用场景

- 适合处理不紧急的任务，如日志记录、分析、预加载、缓存更新等，这些任务可以在不影响用户体验的情况下执行。
- 可以用于渐进增强（progressive enhancement），在不影响主线程性能的情况下增加功能。

### 使用示例

```javascript
function myNonUrgentTask(deadline) {
  // 还有剩余时间，且没有超时，执行任务
  while (
    (deadline.timeRemaining() > 0 || deadline.didTimeout) &&
    tasks.length > 0
  ) {
    performTask(tasks.shift());
  }
  if (tasks.length > 0) {
    requestIdleCallback(myNonUrgentTask);
  }
}

requestIdleCallback(myNonUrgentTask);
```

## Proxy 和 defineProperty

Proxy 拦截的是对象的所有基础操作，而 defineProperty 只是其中的一个基础操作

## this 绑定规则

### 默认绑定

this 指向全局对象，严格模式下为 undefined

严格模式注意点：

```javascript
function foo() {
  console.log(this.a);
}

var a = 2;

(function () {
  "use strict";

  foo(); // 2
})();
```

```javascript
"use strict";
function foo() {
  console.log(this.a);
}

var a = 2;

(function () {
  foo(); // TypeError: Cannot read properties of undefined (reading 'a')
})();
```

### 隐式绑定

对象属性引用链中只有最后一层会影响调用位置

```javascript
function foo() {
  console.log(this.a);
}

var obj2 = {
  a: 42,
  foo,
};

var obj1 = {
  a: 2,
  obj2,
};

obj1.obj2.foo(); // 42
```

### 显示绑定

使用 call、apply、bind

- 第一个参数用来绑定 this，后面的参数用来传参
- 第一个参数为 null 或 undefined 时，this 指向全局对象
- 第一个参数为原始值时，会自动转换为对应的包装类型（`new String() new Number()`），

```javascript
function foo() {
  console.log(this.a);
}

var obj = {
  a: 2,
};

foo.call(obj); // 2
```

#### 硬绑定

函数内部执行绑定函数，`bind`原理就是如此

```javascript
function foo() {
  console.log(this.a);
}
var obj = { a: 2 };
foo.bind(obj)(); // 2
```

```javascript
function foo(something) {
  return this.a + something;
}

var obj = {
  a: 2,
};

var bar = function () {
  return foo.apply(obj, arguments);
};

var b = bar(3);
console.log(b); // 5

// 不再生效
bar.call({ a: 4 }, 5); // 7
```

#### API 调用的“上下文”

一些内置函数或第三方函数都提供了一个可选的参数，通常被称为“上下文”（context），其作用和 bind(..) 一样，确保你的回调函数使用指定的 this。

```javascript
const obj = { a: 1 };
[1, 2, 3].forEach(function (item, index, arr) {
  console.log(this.a); // 1, this:obj
}, obj);
```

### new 绑定

使用 new 来调用函数，或者说发生构造函数调用时，会自动执行下面的操作。

1. 创建（或者说构造）一个全新的对象。

2. 这个新对象会被执行[[原型]]连接。

3. 这个新对象会绑定到函数调用的 this 。

4. 如果函数没有返回其他对象，那么 new 表达式中的函数调用会自动返回这个新对象。

```javascript
function foo(a) {
  this.a = a;
}

var bar = new foo(2);

console.log(bar.a); // 2
```

### 优先级

new 绑定 > 显示绑定 > 隐式绑定 > 默认绑定

### 绑定例外

this 被忽略，建议用更安全的方式替代 null，如`Object.create(null)`

```javascript
function foo(a, b) {
  console.log("a:" + a + ", b:" + b);
}

// 把数组“展开”成参数
foo.apply(null, [2, 3]); // a:2, b:3
```

#### 软绑定

- 实现

```javascript
if (!Function.prototype.softBind) {
  Function.prototype.softBind = function (obj) {
    var fn = this;
    // 捕获所有 curried 参数
    var curried = [].slice.call(arguments, 1);
    var bound = function () {
      return fn.apply(
        !this || this === (window || global) ? obj : this,
        curried.concat.apply(curried, arguments)
      );
    };
    bound.prototype = Object.create(fn.prototype);
    return bound;
  };
}
```

- 应用

```javascript
function foo() {
  console.log("name: " + this.name);
}

var obj = { name: "obj" },
  obj2 = { name: "obj2" },
  obj3 = { name: "obj3" };

var fooOBJ = foo.softBind(obj);

fooOBJ(); // name: obj

obj2.foo = foo.softBind(obj);
obj2.foo(); // name: obj2 <---- 看！！！

fooOBJ.call(obj3); // name: obj3 <---- 看！

setTimeout(obj2.foo, 10);
// name: obj   <---- 应用了软绑定
```

## setTimeout

- 时间参数含有隐式类型转换，会将非数值转换为数值，如果为 NaN，则为 0

## Date.now() & performance.now()

### Date.now()

- 返回当前时间距离 1970 年 1 月 1 日 00:00:00 UTC 的毫秒数
- 与 new Date().getTime()等价

### performance.now()

- 返回从 time origin 之后到当前调用时经过的时间
- 精度与浏览器有关，最高为微秒级别，但为了提供对时序攻击和指纹的保护，精度可能会降低
- 在性能分析中，可以用来计算函数执行时间

> 时序攻击(timing attacks)：通过测量某些操作的执行时间，推断出浏览器内部运行的情况，从而获得敏感信息。比如，测量某个网站的登录操作，可以推断出用户的账户名和密码。为了防止这种攻击，浏览器会采取各种措施，降低 performance.now()的精度，从而防止对内部操作的推断。

## isNaN 和 Number.isNaN 的区别

- isNaN 会将参数转换为数值，如果不能转换为数值，则返回 true
- Number.isNaN 不会转换参数，只有参数为 NaN 时，才返回 true

## 数字转金额

1. toLocaleString

```javascript
let number = 1234567.89;
let formattedNumber = number.toLocaleString("zh-CN", {
  style: "currency",
  currency: "CNY",
});
console.log(formattedNumber); // ¥1,234,567.89
```

2. Intl.NumberFormat

```javascript
let number = 1234567.89;
let formatter = new Intl.NumberFormat("en-US", {
  style: "currency",
  currency: "USD",
});
let formattedNumber = formatter.format(number);
console.log(formattedNumber); // $1,234,567.89
```

3. 正则表达式

```javascript
function formatNumberToCurrency(number) {
  return number.toFixed(2).replace(/\d(?=(\d{3})+\.)/g, "$&,");
}

let number = 1234567.89;
let formattedNumber = formatNumberToCurrency(number);
console.log(formattedNumber); // 1,234,567.89
```

## 跨域解决方案

1. JSONP

原理：利用 script 标签不受同源策略的影响，将回调函数名称作为参数传递
优点：实现简单
缺点：只能 get 请求；容易引起 XSS 攻击，服务器端应做好验证；不支持错误处理

2. CORS

它通过在 HTTP 请求和响应中添加特定的头部字段，实现了跨域资源共享

3. 代理服务器，node+express

4. websocket 可以绕过同源限制
5. iframe 和 postMessage
6. nginx

## WeakMap 和 WeakSet

### WeakMap

使用：`const weakMap = new WeakMap()`
特点：
键必须是引用类型
弱引用，在键值不再被引用时被回收，从而防止内存泄漏
不可枚举，不可遍历

### WeakSet

使用：`const weakSet = new WeakSet()`
特点：
只能存储对象
弱引用，如果一个对象仅被 WeakSet 引用，那么这个对象是可以被垃圾回收的
不可能枚举，不可遍历

### 用途

1. 缓存计算结果
2. 处理 Dom 节点

## null 和 undefined 的区别

null: 变量没有值
undefined: 变量未赋值

##
