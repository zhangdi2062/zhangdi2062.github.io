---
title: js手写专辑
tags:
  - js
categories:
  - 前端
date: 2024-03-03 16:32:37
---

## 实现instanceof

内部实现

```js
function _instanceof(left, right) {
  if (
    right != null &&
    typeof Symbol !== "undefined" &&
    right[Symbol.hasInstance]
  ) {
    return !!right[Symbol.hasInstance](left);
  } else {
    return left instanceof right;
  }
}
```

手写实现

```js
function _instanceof(left, right) {
    if (typeof right !== function) {
        throw new TypeError(`Right-hand side of 'instanceof' is not an object`)
    }
    if (!left || (typeof left !== 'object' && typeof left !== 'function')) return false;
    left = Object.getPrototypeOf(left);
    right = right.prototype;
    while (left) {
        if (left === right) {
            return true;
        }
        left = Object.getPrototypeOf(left);
    }
    return false;
}
```

## 将class转换为function

1. `class`在严格模式下 
2. 类不能直接调用，如`MyFn()`
3. 类中的函数不能实例化，如`a = new MyFn(); new a.sayHi();`

```js
class MyFn {
  constructor(name) {
    this.name = name;
  }
  sayHi() {
    console.log("hi");
  }
}

// 严格模式
("use strict");
function MyFn(name) {
  // 验证this指向
  if (!(this instanceof MyFn1)) {
    throw new TypeError(
      `Class constructor MyFn cannot be invoked without 'new'`
    );
  }
  this.name = name;
}
// MyFn.prototype.sayHi = function() {
//     console.log('hi')
// }
Object.defineProperty(MyFn.prototype, "sayHi", {
  value: function () {
    if (!(this instanceof MyFn)) {
      throw new TypeError(`sayHi is not a constructor`);
    }
    console.log("hi");
  },
  enumerable: false,
});

```
## Promise构造器的实现

```js
const PENDING = 'pending';
const FULFILLED = 'fulfilled';
const REJECTED = 'rejected';

class MyPromise {
    constructor(executor) {
        const resolve = (result) => {
            this.#changeStatus(FULFILLED, result);
        };
        const reject = (result) => {
            this.#changeStatus(REJECTED, result);
        };
        try {
            executor(resolve, reject);
        } catch (error) {
            reject(error);
        }
    }
    #status = PENDING;
    #result = undefined;

    #changeStatus(status, result) {
        // 状态一旦改变，就不能再次改变
        if (this.#status !== PENDING) {
            return;
        }
        this.#status = status;
        this.#result = result;
    }
}

const myPromise = new MyPromise((resolve, reject) => {
    resolve(111);
});

console.log(myPromise);
```

### 实现then方法

```javascript
class MyPromise {
  #handlers: [];
  #runOne(callback, resolve, reject) {
    const settled = this.#status === FULFILLED ? resolve : reject;
    if (typeof callback === "function") {
      try {
        const result = callback(this.#result);
        settled(result);
      } catch (error) {
        reject(error);
      }
    } else {
      settled(this.#result);
    }
  }
  #run() {
    if (this.#status === PENDING) return;
    while (this.#handlers.length) {
      const { onFulfilled, onRejected, resolve, reject } =
        this.#handlers.shift();
      if (this.#status === FULFILLED) {
        this.#runOne(onFulfilled, resolve, reject);
      } else if (this.#status === REJECTED) {
        this.#runOne(onRejected, resolve, reject);
      }
    }
  }
  then(onFulfilled, onRejected) {
    return new MyPromise((resolve, reject) => {
      this.#handlers.push({
        onFulfilled,
        onRejected,
        resolve,
        reject,
      });
      this.#run();
    });
  }
}
```


## flat 和 flatDeep

```javascript
function flat(arr) {
    return Object.assign(...arr);
}

function flatDeep(arr) {
    return arr.reduce((acc, cur) => {
        return acc.concat(Array.isArray(cur) ? flatDeep(cur) : cur);
    }, []);
}

function flatDepth(arr, dep = 1) {
  return d > 0
        ? arr.reduce(
            (acc, val) =>
            acc.concat(Array.isArray(val) ? flatDeep(val, d - 1) : val),
            []
        )
        : arr.slice();
}
```

## 防抖和节流

防抖

```javascript
function debounce(callback, time) {
  let timer = null;
  return function (...args) {
    clearTimeout(timer);
    timer = setTimeout(() => {
        callback.apply(this, args);
    }, time);
  }
}
const fn = debounce(() => {
  console.log('重置窗口大小')
}, 500)
window.addEventListener('resize', fn);
```

```javascript
// 使用customRef实现防抖
<input v-model="text"></input>
<p>{{text}}</p>

let text = debounceRef('', 500)

function debounceRef((value, delay = 1000)) {
  customRef((track, trigger) => {
    let timer = null;
    return {
      get() {
          track();
          return value;
      },
      set(val) {
        clearTimeout(timer);
        timer = setTimeout(() => {
          value = val;
          trigger();
        }, delay)
      }
    }
  })
}

```

节流

```javascript
function throttle(callback, time) {
  let lastCall = Date.now();
  return function() {
    const curTime = Date.now()
    if (curTime - lastCall > time) {
      lastCall = curTime;
      callback.apply(this, args);
    }
  }
}
```