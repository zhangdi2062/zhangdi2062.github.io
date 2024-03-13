---
title: vue知识
tags:
  - vue
categories:
  - 前端
date: 2024-03-03 16:21:57
---




## 效率提升

1. 静态提升
2. 模板转字符串
3. 缓存事件函数
4. Block Tree
5. PatchFlag

# vue2和vue3的区别

作为一名高级前端开发者，我可以为您详细解释Vue 2和Vue 3之间的一些主要区别。Vue 3是Vue.js的下一个主要版本，它在Vue 2的基础上进行了许多改进和新功能的添加。以下是Vue 2和Vue 3之间的一些关键区别：

1. **Composition API**：
   - Vue 3引入了Composition API，这是一组新的API，允许开发者更灵活地组织和重用逻辑。这与Vue 2中的Options API（选项API）形成对比，后者要求开发者在组件的`data`、`methods`、`computed`和`watch`等选项中组织代码。

2. **性能提升**：
   - Vue 3在性能上进行了显著优化，包括更快的虚拟DOM算法、更小的体积以及更好的类型推断，这使得Vue 3在渲染大型应用时更加高效。

3. **更好的TypeScript支持**：
   - Vue 3从一开始就设计了对TypeScript的更好支持，而不是像Vue 2那样需要额外的类型定义文件。

4. **Fragment, Teleport, Suspense**：
   - Vue 3允许组件返回多个根节点（Fragment），这是Vue 2所不支持的。
   - Teleport组件允许将子节点传送到DOM的另一个位置，而不是默认的父组件内部。
   - Suspense组件提供了一种处理异步操作的方式，它允许开发者在等待异步操作完成时显示加载状态。

5. **响应式系统的重写**：
   - Vue 3使用Proxy重写了响应式系统，这使得Vue 3在追踪依赖和更新视图时更加高效。

6. **更小的体积**：
   - Vue 3的体积比Vue 2更小，这有助于提高页面加载速度。

7. **更好的模块化**：
   - Vue 3的代码库更加模块化，这使得开发者可以根据需要导入特定的功能，从而优化最终的打包大小。

8. **全局API的变化**：
   - Vue 3对全局API进行了一些调整，例如，`Vue.config`和`Vue.util`等。

9. **自定义渲染器API**：
   - Vue 3提供了创建自定义渲染器的能力，这意味着开发者可以为Vue创建不同的渲染环境，比如服务器端渲染或原生移动应用。

10. **生命周期钩子的改进**：
    - Vue 3中的生命周期钩子更加一致，例如，`onBeforeMount`、`onMounted`、`onBeforeUpdate`、`onUpdated`、`onBeforeUnmount`和`onUnmounted`
    - Vue 3还引入了新的生命周期钩子，例如`onRenderTracked`和`onRenderTriggered`，它们允许开发者在追踪和触发渲染时执行自定义逻辑。
    - Vue 2生命周期为`beforeCreate`和`created`，Vue 3中改为`setup`。`beforeMount`、`mounted`、`beforeDestroy`、`destroyed`

这些区别使得Vue 3在许多方面都比Vue 2更加强大和灵活。然而，值得注意的是，Vue 3的这些新特性和改进也意味着从Vue 2迁移到Vue 3可能需要一些代码重构。

# vue响应式原理

Vue.js 的响应式原理是其核心特性之一，它允许开发者以声明式的方式将数据与视图绑定。Vue 的响应式系统主要依赖于两个关键技术：观察者模式（Observer Pattern）和依赖收集（Dependency Collection）。

以下是Vue响应式原理的详细介绍：

1. **数据劫持（Data Proxy）**：
   - Vue.js 通过使用 ES5 的 `Object.defineProperty` 函数，将数据对象的所有属性转换为 getter/setter。这样，每当属性被访问（getter 被调用）或属性值被修改（setter 被调用）时，Vue 都能够知道。

2. **依赖收集（Dependency Collection）**：
   - 当组件渲染时，它会访问响应式数据的属性，这时 Vue 会记录当前正在渲染的组件作为依赖。这个过程称为依赖收集。每个组件都会维护一个依赖项列表，这些依赖项是组件需要观察的响应式数据。

3. **观察者模式（Observer Pattern）**：
   - Vue 实现了一个观察者模式，其中每个组件实例都是一个观察者。当响应式数据发生变化时，Vue 会通知所有订阅了这些数据的组件，触发它们的更新。

4. **发布-订阅（Pub-Sub）**：
   - Vue 的响应式系统内部实现了一个发布-订阅模式。当响应式数据的 setter 被调用，即数据发生变化时，Vue 会遍历该数据的所有订阅者（即依赖于该数据的组件），并通知它们进行更新。

5. **批量异步更新（Async Queue）**：
   - Vue 在检测到数据变化时并不会立即更新DOM，而是把这些变化放到一个异步队列中。在下一个事件循环（tick）中，Vue 会清空队列，并执行实际的DOM更新。这种机制可以避免不必要的重复渲染，提高性能。

6. **Vue 3中的Proxy**：
   - 在Vue 3中，响应式系统使用了 ES6 的 Proxy 替代了 Vue 2 中的 `Object.defineProperty`。Proxy 提供了更全面的拦截特性，允许 Vue 监听更多类型的数据变化，如数组索引和长度的变化，以及更复杂的对象属性变化。

通过这些机制，Vue 能够确保视图与数据保持同步。当数据发生变化时，Vue 会自动更新DOM，而开发者无需手动操作DOM。这种数据驱动的视图更新方式大大简化了复杂应用的开发。

# vue的路由模式

## `hash`

优点：
- 路由切换不会向服务器发送请求
- 页面切换不会刷新页面，用户体验好

缺点：
- 有#号不美观

实现：通过监听`hashChange`事件来实现前端路由。

触发：浏览器的前进后退；`location.hash`的改变；`a`标签的点击事件。

## `history`

优点：
- URL更美观，没有#号
- 不会有#号
- 可以通过`pushState`和`replaceState`来实现前端路由
- 可以通过`popstate`事件来监听路由变化
- 可以通过`history.back()`和`history.forward()`来实现前进和后退
- 可以通过`history.go()`来实现任意页面跳转
- 可以通过`history.replaceState()`来实现页面跳转后不留下历史记录
- 可以通过`history.pushState()`来实现页面跳转后留下历史记录
- 可以通过`history.state`来获取当前页面的状态
- 可以通过`history.length`来获取历史记录的长度
- 可以通过`history.scrollRestoration`来设置页面跳转后滚动条的位置
- 可以通过`history.onpopstate`来监听`popstate`事件
- 可以通过`history.onpushstate`来监听`pushstate`事件
- 可以通过`history.onreplacestate`来监听`replacestate`事件
- 可以通过`history.onbeforeunload`来监听页面关闭事件
- 可以通过`history.onunload`来监听页面卸载事件
- 可以通过`history.onpageshow`来监听页面显示事件
- 可以通过`history.onpagehide`来监听页面隐藏事件


## `abstract`: 

# vue3

## 父子组件传参

- Vue3

### defineProps

```javascript
// 子组件接收父组件传过来的值
const props = defineProps({
   title: {
      type: String,
      default: '默认标题'
   }
})
// 使用
props.title

```

### defineEmits

```javascript
// 子组件给父组件传值
// const emit = defineEmits([ 'changeTitle', 'on-click' ]);
// ts写法
const emit = defineEmits<{
   (e: 'changeTitle', title: string): void
}
// 使用
emit('changeTitle', '新标题')
```

### defineExpose
```js
// 子组件暴露方法或属性给父组件调用
const methods = defineExpose({
   changeTitle() {}
})

// 父组件接收
<Child ref="child" />
// ts写法
const child = ref<InstanceType<typeof Child>>();
child.value.changeTitle
```


- Vue2

```javascript
export default {
   props: {
      title: {}
   },
   methods: {
      // 使用
      changeTitle() {
         this.title
      }
   }
}

```

## 计算属性

```javascript
import { computed, ref } from 'vue'
let firstName = ref('张')
let lastName = ref('三')
// 第一种 选项式写法
computed({
   get() {
      return firstName.value + '-' + lastName.value
   },
   set(val) {
      const names = val.split('-')
      [firstName.value, lastName.value] = names
   }
})

// 第二种 函数式写法，只支持getter函数，不能修改name的值
const name = computed(() => firstName.value + '-' + lastName.value)
```

## watch监听器

```javascript
import { watch, ref } from 'vue'
let message = ref('hello');
watch(message, (newValue, oldValue) => {
   console.log(newValue, oldValue)
})
// 监听多个值
watch([message, other], (newValue, oldValue) => {
   console.log(newValue, oldValue) // [message的新值, other的新值], [message的旧值, other的旧值]
})
// 深度监听，如果是ref对象，需要加上deep:true，而reactive对象默认就是深度监听的
watch(message, (newValue, oldValue) => {}, { deep: true })
// 监听一个对象的某个属性，变成函数返回值
watch(() => message.value.name, (newValue, oldValue) => {})
// 立即执行
watch(message, (newValue, oldValue) => {}, { immediate: true })
// 调用时机，pre表示在组件更新之前同步触发回调，sync表示同步执行，Vue 进行任何更新之前触发，post表示在组件更新之后同步触发回调
watch(message, (newValue, oldValue) => {}, { flush: 'pre' }) // 在依赖变更之后同步触发回调
```

## watchEffect 侦听器

```javascript
import { watchEffect, ref } from 'vue'
let message = ref('hello');
// 监听message的变化，自动执行回调函数，不需要手动调用
watchEffect(() => {
   console.log(message.value)
})
// 侦听器回调中能访问被 Vue 更新之后的所属组件的 DOM
watchPosEffect(() => {})
// 停止监听器，同步创建监听器时，无需手动停止，异步创建时，需要手动停止
const stop = watchEffect(() => {})
stop()

// 开发环境上使用
// onTrack 将在响应属性或引用作为依赖项被跟踪时被调用。
// onTrigger 将在侦听器回调被依赖项的变更触发时被调用。
watchEffect(() => {

}, {
   onTrack() {},
   onTrigger() {}
})
```

## 局部组件和递归组件和全局组件

### 局部组件

直接在组件中引用

### 递归组件

组件内部引用自己
```js
// Tree.vue，递归组件内部使用名称和组件名相同

<template>
   <div v-for="item in data" :key="item.id" :data="item">
      <span>item.name</span>
      <Tree v-if="item?.children?.length"></Tree>
   </div>
</template>

// 如果想改名，可以使用name属性
defineOptions({
   name: 'MyTree'
})
```
