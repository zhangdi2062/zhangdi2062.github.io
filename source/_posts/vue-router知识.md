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