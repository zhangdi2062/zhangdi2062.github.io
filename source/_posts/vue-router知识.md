---
title: vue-router知识
tags:
  - js
  - vue
categories:
  - 前端
date: 2024-03-22 16:11:55
---

# vue3

## 引入

```javascript
// main.ts
import { createApp } from 'vue'
import { createRouter, createWebHistory } from 'vue-router';
import App from './App.vue'
import routes from './router';

const router = createRouter({
    history: createWebHistory(),
    routes
})

const app = createApp(App)
app.use(router);
app.mount('#app')

```

router.ts

```ts
import type {RouteRecordRaw, RouteComponent} from 'vue-router';

function asyncImport(componentName:string):RouteComponent {
    return () => import(`../views/${componentName}.vue`)
}

const routes: RouteRecordRaw[] = [
    {
        path: '/',
        name: 'home',
        component: asyncImport('Home')
    },
    {
        path: '/poems',
        name: 'poems',
        component: asyncImport('PoemList'),
    },
    {
        path: '/poems/:id',
        name: 'poemById',
        component: asyncImport('Poem'),
    }
]

export default routes;
```

## 使用

```ts
import { useRouter } from 'vue-router'

const router = useRouter()  
const getDetail = (item) => {
    router.push({
        name: 'poemById',
        // 地址参数
        params: { id: item.title },
        // 地址后拼接的查询参数
        query: {item},
    })
}
```

```ts

```

## 导航守卫

1. 导航被触发
2. 在失活的组件里调用`beforeRouteLeave`守卫，可以访问组件实例 `this`
3. 调用全局的`beforeEach`守卫
4. 在复用的组件里调用`beforeRouteUpdate`守卫，比如在 `/users/1` 和 `/users/2` 之间跳转的时候，渲染的是同一个组件，可以访问组件实例 `this`
5. 在路由配置里调用 `beforeEnter`，路由独享守卫，例如
    ```javascript
    const routes = [
    {
        path: '/users/:id',
        component: UserDetails,
        beforeEnter: (to, from) => {
        // reject the navigation
        return false
        },
    },
    ]
    ```
6. 解析异步路由组件
7. 在被激活的组件里调用 `beforeRouteEnter`，不能访问组件实例 `this`
8. 调用全局的`beforeResolve`守卫
9. 导航被确认
10. 调用全局的`afterEach`钩子
11. 触发 DOM 更新
12. 调用 `beforeRouteEnter` 守卫中传给 `next` 的回调函数，创建好的组件实例会作为回调函数的参数传入。
    ```javascript
    export default {
        data() {},
        beforeRouteEnter(to, from, next) {
            console.log(this); // undefined
            // 传递一个回调函数给 `next`
            next(vm => {
                console.log(vm); // 组件实例
            });
        },
    };
    ```