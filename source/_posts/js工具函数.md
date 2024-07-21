---
title: js工具函数
tags:
  - js
categories:
  - 前端
date: 2024-03-03 16:26:01
---

## 给 fetch 和 xhr 请求加 token

应用场景：产品中访问静态文件（如 js、json）不需要身份验证，但在项目中有安全性要求，需要所有请求都加入身份验证

```js
// 处理fetch请求
function hookFetch() {
  var fet = Object.getOwnPropertyDescriptor(window, "fetch");
  Object.defineProperty(window, "fetch", {
    value: function () {
      const [url, content] = arguments;
      const tk = "token";
      if (url.includes("/xxx/") && tk) {
        if (content) {
          if (content.headers) {
            if (!content.headers.tk) {
              content.headers.tk = tk;
            }
          } else {
            Object.assign(content, {
              headers: { tk },
            });
          }
        } else {
          arguments.length = 2;
          arguments[1] = {
            headers: { tk },
          };
        }
      }
      if (!tk) {
        console.error("token不存在");
      }
      return fet.value.apply(this, arguments);
    },
  });
}

// 处理XMLHttpRequest请求
function hookXml() {
  const re = XMLHttpRequest.prototype.open;
  XMLHttpRequest.prototype.open = function (method, url) {
    let res = re.apply(this, [].slice.call(arguments));
    const tk = "token";
    if (url.includes("/xxx/") && tk) {
      this.setRequestHeader("tk", tk);
    }
    if (!tk) {
      console.error("token不存在");
    }
  };
}

hookXml();
hookFetch();
```

## 函数重载

应用场景：同一个函数支持多种参数的调用，并返回对应的结果

```js
function getUsers(...args) {}
getUsers(); // 得到全部数据
getUsers(1); // 获取第一页数据
getUsers(1, 20); // 获取第一页前20条数据
getUsers("男"); // 获取性别为男的数据
```

具体实现：

```js
// 封装
function createOverload() {
  const map = new Map();
  function overload(...keys) {
    const types = keys.map((it) => typeof it).join(",");
    const fn = map.get(types);
    if (!fn) {
      throw new TypeError("未找到对应实现");
    }
    return fn.apply(this, keys);
  }
  overload.addImpl = function (...args) {
    const fn = args.pop();
    if (typeof fn !== "function") {
      throw new TypeError("最后一个参数必须是处理函数");
    }
    // 将参数拼接形成key
    map.set(args.join(","), fn);
  };
  return overload;
}

// 实现
const getUsers = createOverload();
getUsers.addImpl(() => {
  console.log("返回所有数据");
});
getUsers.addImpl("number", (n) => {
  console.log("返回第一页数据", n);
});
getUsers.addImpl("number", "number", (n1, n2) => {
  console.log("返回第一页前20条数据", n1, n2);
});
getUsers.addImpl("string", (str) => {
  console.log("返回xx数据", str);
});

// 使用
getUsers(); // 得到全部数据
getUsers(1); // 获取第一页数据
getUsers(1, 20); // 获取第一页前20条数据
getUsers("男"); // 获取性别为男的数据
```

## 封装 v-resize 自定义指令

应用场景：监控元素尺寸的变化，拖拽重新渲染图表，

具体实现：封装 vue 自定义指令

```js
// WeakMap不影响垃圾回收
const map = new WeakMap();
// 尺寸观察者
const ob = new ResizeObserver(entries => {
    for (const entry of entries) {
        const { target } = entry;
        // 获取dom的回调函数
        const handler = map.get(target);
        if (handler) {
            // 以下代码可按需求扩展
            const box = entry.borderBoxSize[0];
            handler({width: box.inlineSize, height: box.blockSize});
        }
    }
})

export default {
    mounted(el, binding) {
        // 监视尺寸变化
        ob.observe(el);
        map.set(el, binding.value);
    },
    unmounted() {
        // 取消监听
        ob.unobserve(dom);
    }

}

// 使用
<div v-size-ob="change"></div>
function change(w,h) {
    updateChart(w, h)
}
```

## 触发迅雷下载

应用场景：调用迅雷的下载

具体实现：

```js
// 前提，a标签的href值为普通下载链接地址
// 给a元素加个data-thunder属性，以便筛选需要训练下载的标签
const links = document.querySelectorAll("a[data-thunder]");
for (const link of links) {
  const href = `AA${link.href}ZZ`;
  const base64 = btoa(href);
  link.href = `thunder://${base64}`;
}
```

## 消除异步的传染性

应用场景：当函数 A 为异步请求时，函数 B 使用了函数 A 则函数 B 也需异步，函数 C 使用函数 B 也需要异步，导致所有依赖函数都要加上 async await

解决思路：消除始作俑函数的异步，参考 react 中做法，在异步函数中抛出错误，等待异步完成后，将结果缓存下来，重新执行一开始的函数

具体实现：

```js
// 原代码
async function getUser() {
  return await fetch("https://api.github.com/users/1");
}

async function m() {
  return await getUser();
}

async function main() {
  const user = await m();
  console.log(user);
}
main();
```

```js
// 实现代码
function getUser() {
  return fetch("https://api.github.com/users/1");
}

function m() {
  return getUser();
}

function main() {
  console.log("打印");
  const user = m();
  console.log(user);
}

function run(func) {
  // 1. 改动fetch
  // 2. 执行func
  // 3. 还原fetch
  const _fetch = window.fetch;
  let catchData = {
    status: "pending",
    value: null,
  };
  function newFetch(...args) {
    if (catchData.status === "fulfilled") {
      return catchData.value;
    }
    if (catchData.status === "rejected") {
      throw catchData.value;
    }
    const promise = _fetch(...args)
      .then((res) => res.json())
      .then((data) => {
        catchData = {
          status: "fulfilled",
          value: data,
        };
      })
      .catch((err) => {
        catchData = {
          status: "rejected",
          value: err,
        };
      });
    throw promise;
  }
  window.fetch = newFetch;
  try {
    func();
  } catch (e) {
    if (e instanceof Promise) {
      e.finally(() => {
        window.fetch = newFetch;
        func();
        window.fetch = _fetch;
      });
    }
  }
  window.fetch = _fetch;
}

run(main);
```

## 给 fetch 添加超时功能

```js
function createRequestWithTimeout(time = 5000) {
  return function (url, options) {
    return new Promise((resolve, reject) => {
      // 因为promise的状态只会改变一次，所以fetch和setTimeout并列即可
      // 超时后取消请求
      const controller = new AbortController();
      options = options || {};
      if (options.signal) {
        const { signal } = options;
        signal.addEventListener("abort", () => {
          controller.abort();
        });
      } else {
        options.signal = controller.signal;
      }
      fetch(url, options).then(resolve, reject);
      setTimeout(() => {
        reject(new Error("请求超时"));
        controller.abort();
      }, time);
    });
  };
}

// 使用
const request = createRequestWithTimeout(3000);
request("url", {});
```

## 判断当前浏览器环境

```javascript
// 是否是mac系统
export const isMac = navigator.userAgent.includes("Mac OS X");
// 判断是否是safair浏览器
const ua = navigator.userAgent.toLowerCase();
export const isSafari = /safari/.test(ua) && !/chrome/.test(ua);
// 判断是否是谷歌浏览器
const isChrome = ua.match(/chrome\/([\d\.]+) safari\/([\d\.]+)$/);
export function isChrome() {
  // 其他浏览器特有的mimeTypes
  const mimeTypeList = [
    "application/vnd.chromium.remoting-viewer", // 360
    "application/vnd.adobe.pdfxml", // 360,2345
    "application/hwpta.itrushwpta", // 360
    "application/sogou-native-widget-plugin", // 搜狗
    "application/x-shockwave-flash", // 猎豹
    "application/futuresplas", // 猎豹
  ];
  return (
    isChrome &&
    !![...navigator.mimeTypes].find((item) =>
      mimeTypeList.find((type) => item.type.toLowerCase() === type)
    )
  );
}
// 判断是否是edge浏览器
const isOtherChrome = ua.match(/chrome\/([\d\.]+) safari\/([\d\.]+) \S+/);
export const isEdge =
  isOtherChrome && isOtherChrome && isOtherChrome[0].indexOf("edg") > -1;
```

## 实现一个倒计时

- setInterval

```javascript
function countDown(time) {
  let timer = setInterval(() => {
    if (time === 0) {
      clearInterval(timer);
      return;
    }
    time--;
    console.log(time);
  }, 1000);
}
```

- setTimeout

```javascript
function countDown(time) {
  let timer = setTimeout(() => {
    clearTimeout(timer);
    if (time > 0) {
      time--;
      console.log(time);
      countDown(time);
    }
  }, 1000);
}
```

- requestAnimationFrame

```javascript
function countDown(time, lastTime = 0) {
  let timer = requestAnimationFrame(() => {
    cancelAnimationFrame(timer);
    if (time === 0) {
      return;
    }
    const now = Date.now();
    if (lastTime === 0) {
      lastTime = now;
    }
    if (now - lastTime >= 1000) {
      lastTime = now;
      time--;
      console.log(time);
    }
    countDown(time, lastTime);
  });
}
```

- 结合 setTimeout 和 requestAnimationFrame，利用时间差实现较精准的倒计时

```javascript
function countDown(time) {}
```

- 使用 web worker 实现

## 使用高阶函数处理大数据量

举例：点击按钮时，插入 10 万条 dom 数据到页面上

解决：正常 for 循环创建会导致页面卡顿并渲染不出来

思路：使用分片执行，在合适的时机一部分一部分的渲染，并将函数封装成通用函数

[参考视频【分时函数的封装【渡一教育】】 ](https://www.bilibili.com/video/BV1jw4m1a7Rf/?share_source=copy_web&vd_source=1c49952310d342499db5babb39e3b6b9)

```javascript
/**
 * 需求：有一些任务要执行很多次，但不能阻塞页面
 * 需要提供什么：
 *  1. 执行次数/数据
 *  2. 执行任务
 * 可扩展性：
 *  1. 执行时机
 *  2. 每次执行多少
 *  3. 执行完成后操作
 */

// 注：这里简化了代码优化的过程，直接显示最终结果，具体可详见视频

/**
 * @description 任务分片执行函数
 * @param {Array|Number} data 执行次数/数据
 * @param {Function} taskHandler 任务处理函数
 * @param {Function} scheduler 执行时机
 * @param {Function} done 执行完成后操作
 */
function performChunk(data, taskHandler, scheduler, done) {
  if (typeof data === "number") {
    data = {
      length: data,
    };
  }
  const { length } = data;
  if (length === 0) return;
  // 任务索引
  let index = 0;

  function _run() {
    if (index >= length) {
      done && done();
      return;
    }
    // 执行任务
    scheduler((goOn) => {
      while (goOn() && index < length) {
        taskHandler(index, data[index]);
        index++;
      }
      _run();
    });
  }
  _run();
}
// 为浏览器环境提供默认的执行时机
function browserPerformChunk(data, taskHandler, done) {
  function scheduler(taskFn) {
    requestIdleCallback((idle) => {
      taskFn(() => idle.timeRemaining() > 0);
    });
  }
  performChunk(data, taskHandler, scheduler, done);
}

// 使用
const arr = new Array(100000).fill(0).map((_, index) => index + 1);

function taskHandler(data, i) {
  const dom = document.createElement("div");
  dom.innerText = i + 1;
  document.body.appendChild(dom);
}
browserPerformChunk(arr, taskHandler);
```
