---
title: js知识
tags:
  - js
categories:
  - 前端
date: 2024-03-01 21:00:17
---

## async 和 defer 和 type=module

```html
<head>
  <script src="url"></script>
</head>
```

下载和解析js文件会阻塞html解析

```html
<head>
  <script src="url" async></script>
</head>
```

下载不会阻塞html解析，下载完成后会立马解析js文件，阻塞html渲染

```html
<head>
  <script src="url" defer></script>
  <script src="url" type="module"></script>
</head>
```

下载和解析js文件都不会阻塞html解析，等待页面渲染完成后再解析js

相关事件：`DOMContentLoaded` 和 `load`

DOMContentLoaded 在第一和第三种情况下运行完成后会触发，异步加载不会触发

load事件在整个文档加载完成后触发

## 类型转换规则

### 原始 -> 数字

true: 1
false: 0
null: 0
undefined: NaN
string: 空字符串: 0
        去掉引号，不是数字就是NaN
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

## for in和for of

for in

1. 遍历对象的可枚举属性，包括对象自身的属性以及继承的属性
2. 不保证遍历顺序

for of 

1. 遍历可迭代对应的属性值
2. 迭代顺序与索引顺序一致
3. 对象不能迭代

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

函数的二义性：普通函数通过function构造，一方面可以直接调用，另一方面可以实例化，给调用者如何调用造成了困难，基本上通过开发常识来区分，比如函数名大写的构造函数

箭头函数和普通的函数的区别：

1. 没有`this` `super` `arguments`，因为没有this，故无法通过任何手段绑定`this`
2. 不能使用`new`调用
3. 没有原型

## 属性描述符

```js
const obj = {
  property1: 42,
};

const descriptors1 = Object.getOwnPropertyDescriptors(obj);

const property = descriptors1.property1;
property.value // 属性值 42
property.writable // 是否可修改 true
property.enumerable // 是否可枚举 true
property.configurable // 属性描述符是否可再次修改
``` 

## cookie

### SameSite

`SameSite`的作用：限制**跨站**请求，决定是否发送即将请求网站的cookie，防止攻击者利用用户的登录状态来执行未经授权的操作。

可选值：

`none`：不做任何限制 需配合`https`使用，可能会有CSRF攻击
使用方式：`Set-Cookie: ..;Secure;SameSite=None`

`Lax`：默认值，阻止发送Cookie，但对超链接`<a href=""></a>`放行

`Strict`：阻止发送Cookie


### Domain 站点

使用：`Set-Cookie: ..;domain=www.a.com`

> 判断是否跨站：站点名一致为同站，一般为后面的域名如 `a.foo.com` & `b.c.foo.com`。\n 但要排除公共后缀，如`a.github.io` & `b.github.io`为跨站，因为`github.io`为公共后缀，a和b不同站。公共后缀的查询网址：https://publicsuffix.org/list/public_suffix_list.dat


# Promise

## 含义

Promise，简单说就是一个容器，里面保存着某个未来才会结束的事件（通常是一个异步操作）的结果。从语法上说，Promise 是一个对象，从它可以获取异步操作的消息。

## 特点

1. 对象的状态不受外界影响，只有异步操作的结果，能改变他的状态
2. 状态一旦改变，就不会再变，任何时候都可以得到这个结果

## 缺点

1. 无法取消
2. 如果没有设置回调函数，则内部抛出的错误无法反应到外部
3. 当处于pending状态时，无法得知目前进展到哪一个阶段（刚刚开始还是即将完成）

## 基本用法

一般来说，调用resolve或reject以后，Promise 的使命就完成了，后继操作应该放到then方法里面，而不应该直接写在resolve或reject的后面。所以，最好在它们前面加上return语句，这样就不会有意外。

## Promise.prototype.then()

then方法返回的是一个新的Promise实例（注意，不是原来那个Promise实例）。因此可以采用链式写法，即then方法后面再调用另一个then方法。

## Promise.prototype.catch()

```js
// bad
promise
  .then(function(data) {
    // success
  }, function(err) {
    // error
  });

// good
promise
  .then(function(data) { //cb
    // success
  })
  .catch(function(err) {
    // error
  });
```

上面代码中，第二种写法要好于第一种写法，理由是第二种写法可以捕获前面then方法执行中的错误，也更接近同步的写法（try/catch）。因此，建议总是使用catch()方法，而不使用then()方法的第二个参数。

## Promise.prototype.finally()

finally()方法用于指定不管 Promise 对象最后状态如何，都会执行的操作，并且回调函数不接受任何参数，finally方法总是会返回原来的值

```javascript
// resolve 的值是 undefined
Promise.resolve(2).then(() => {}, () => {})

// resolve 的值是 2
Promise.resolve(2).finally(() => {})
```
### Promise.all()

Promise.all()方法接受一个数组作为参数，p1、p2、p3都是 Promise 实例，如果不是，就会先调用下面讲到的Promise.resolve方法，将参数转为 Promise 实例，再进一步处理。另外，Promise.all()方法的参数可以不是数组，但必须具有 Iterator 接口，且返回的每个成员都是 Promise 实例。

p的状态由p1、p2、p3决定，分成两种情况。

（1）只有p1、p2、p3的状态都变成fulfilled，p的状态才会变成fulfilled，此时p1、p2、p3的返回值组成一个数组，传递给p的回调函数。

（2）只要p1、p2、p3之中有一个被rejected，p的状态就变成rejected，此时第一个被reject的实例的返回值，会传递给p的回调函数。

注意，如果作为参数的 Promise 实例，自己定义了catch方法，那么它一旦被rejected，并不会触发Promise.all()的catch方法。

### Promise.race()

只要p1、p2、p3之中有一个实例率先改变状态，p的状态就跟着改变。那个率先改变的 Promise 实例的返回值，就传递给p的回调函数。

### Promise.allSettled()

用来确定一组异步操作是否都结束了（不管成功或失败），一旦发生状态变更，状态总是fulfilled

返回结果为数组，格式如下

```javascript
// 异步操作成功时
{status: 'fulfilled', value: value}

// 异步操作失败时
{status: 'rejected', reason: reason}
```

### Promise.any()

只要参数实例有一个变成fulfilled状态，包装实例就会变成fulfilled状态；

如果所有参数实例都变成rejected状态，包装实例就会变成rejected状态。

Promise.any()抛出的错误是一个 AggregateError 实例，这个 AggregateError 实例对象的errors属性是一个数组，包含了所有成员的错误。

### Promise.resolve()

将现有对象转为 Promise 对象

（1）参数是一个 Promise 实例

如果参数是 Promise 实例，那么Promise.resolve将不做任何修改、原封不动地返回这个实例。

（2）参数是一个thenable对象

thenable对象指的是具有then方法的对象，比如下面这个对象。

let thenable = {
  then: function(resolve, reject) {
    resolve(42);
  }
};
Promise.resolve()方法会将这个对象转为 Promise 对象，然后就立即执行thenable对象的then()方法。

let thenable = {
  then: function(resolve, reject) {
    resolve(42);
  }
};

let p1 = Promise.resolve(thenable);
p1.then(function (value) {
  console.log(value);  // 42
});
上面代码中，thenable对象的then()方法执行后，对象p1的状态就变为resolved，从而立即执行最后那个then()方法指定的回调函数，输出42。

（3）参数不是具有then()方法的对象，或根本就不是对象

如果参数是一个原始值，或者是一个不具有then()方法的对象，则Promise.resolve()方法返回一个新的 Promise 对象，状态为resolved。

const p = Promise.resolve('Hello');

p.then(function (s) {
  console.log(s)
});
// Hello
上面代码生成一个新的 Promise 对象的实例p。由于字符串Hello不属于异步操作（判断方法是字符串对象不具有 then 方法），返回 Promise 实例的状态从一生成就是resolved，所以回调函数会立即执行。Promise.resolve()方法的参数，会同时传给回调函数。

（4）不带有任何参数

Promise.resolve()方法允许调用时不带参数，直接返回一个resolved状态的 Promise 对象。

### Promise.reject() 

Promise.reject(reason)方法也会返回一个新的 Promise 实例，该实例的状态为rejected。

### Promise.try()

不知道或者不想区分，函数f是同步函数还是异步操作，但是想用 Promise 来处理它。因为这样就可以不管f是否包含异步操作，都用then方法指定下一步流程，用catch方法处理f抛出的错误。一般就会采用下面的写法。

```javascript
const f = () => console.log('now');
(
  () => new Promise(
    resolve => resolve(f())
  )
)();
console.log('next');
```

提案

```javascript
Promise.try(() => database.users.get({id: userId}))
  .then(...)
  .catch(...)
```