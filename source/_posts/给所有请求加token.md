---
title: 给所有请求加token
date: 2023-07-22 22:23:31
tags:
- js
- fetch
- xhr
categories:
- 前端
---

背景：产品中访问静态文件（如js、json）不需要身份验证，但在项目中有安全性要求，需要所有请求都加入身份验证

```js
// 处理fetch请求
function hookFetch() {
    var fet = Object.getOwnPropertyDescriptor(window, "fetch");
    Object.defineProperty(window, "fetch", {
        value: function () {
            const [url, content] = arguments;
            const tk = 'token';
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
                console.error('token不存在');
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
        const tk = 'token';
        if (url.includes("/xxx/") && tk) {
            this.setRequestHeader("tk", tk);
        }
        if (!tk) {
            console.error('token不存在');
        }
    };
}

hookXml();
hookFetch();

```