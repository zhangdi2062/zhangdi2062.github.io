---
title: vue知识
tags:
  - vue
categories:
  - 前端
date: 2024-03-03 16:21:57
---

# 原理

## 效率提升

1. 静态提升：静态节点和属性都被提升到 render 函数外执行
2. 模板转字符串：当编译器遇到大量连续的静态内容，会直接将其编译为一个普通字符串节点
3. 缓存事件函数：将事件处理函数缓存起来，下次 render 时作为参数传递使用
4. Block Tree：在根节点中只记录动态节点进行比较，处理比较复杂
5. PatchFlag：根据动态内容的不同标记不同的数值，例如动态文本是 1，动态类名是 2，文本+类名都动态是 3，故在比对时，只需要对比对应的动态属性即可，不用每一项都比对

## vue2 和 vue3 的区别

Vue 3 是 Vue.js 的下一个主要版本，它在 Vue 2 的基础上进行了许多改进和新功能的添加。以下是 Vue 2 和 Vue 3 之间的一些关键区别：

1. **Composition API**：

   - Vue 3 引入了 Composition API，这是一组新的 API，允许开发者更灵活地组织和重用逻辑。这与 Vue 2 中的 Options API（选项 API）形成对比，后者要求开发者在组件的`data`、`methods`、`computed`和`watch`等选项中组织代码。

2. **性能提升**：

   - Vue 3 在性能上进行了显著优化，包括更快的虚拟 DOM 算法、更小的体积以及更好的类型推断，这使得 Vue 3 在渲染大型应用时更加高效。

3. **更好的 TypeScript 支持**：

   - Vue 3 从一开始就设计了对 TypeScript 的更好支持，而不是像 Vue 2 那样需要额外的类型定义文件。

4. **Fragment, Teleport, Suspense**：

   - Vue 3 允许组件返回多个根节点（Fragment），这是 Vue 2 所不支持的。
   - Teleport 组件允许将子节点传送到 DOM 的另一个位置，而不是默认的父组件内部。
   - Suspense 组件提供了一种处理异步操作的方式，它允许开发者在等待异步操作完成时显示加载状态。

5. **响应式系统的重写**：

   - Vue 3 使用 Proxy 重写了响应式系统，这使得 Vue 3 在追踪依赖和更新视图时更加高效。

6. **更小的体积**：

   - Vue 3 的体积比 Vue 2 更小，这有助于提高页面加载速度。

7. **更好的模块化**：

   - Vue 3 的代码库更加模块化，这使得开发者可以根据需要导入特定的功能，从而优化最终的打包大小。

8. **全局 API 的变化**：

   - Vue 3 对全局 API 进行了一些调整，例如，`Vue.config`和`Vue.util`等。

9. **自定义渲染器 API**：

   - Vue 3 提供了创建自定义渲染器的能力，这意味着开发者可以为 Vue 创建不同的渲染环境，比如服务器端渲染或原生移动应用。

10. **生命周期钩子的改进**：

    - Vue 3 中的生命周期钩子更加一致，例如，`onBeforeMount`、`onMounted`、`onBeforeUpdate`、`onUpdated`、`onBeforeUnmount`和`onUnmounted`
    - Vue 3 还引入了新的生命周期钩子，例如`onRenderTracked`和`onRenderTriggered`，它们允许开发者在追踪和触发渲染时执行自定义逻辑。
    - Vue 2 生命周期不同

11. **diff 算法**

Vue 的 diff 算法是用于在虚拟 DOM（Virtual DOM）更新过程中比较新旧两个虚拟节点树的差异，从而仅对有差异的部分进行真实 DOM 的更新，以提高性能。Vue2 和 Vue3 中的 diff 算法都基于 Snabbdom 库，但在 Vue3 中，diff 算法进行了一些优化，使得性能更高。

以下是对 Vue2 和 Vue3 中 diff 算法的理解：

1. Vue2 diff 算法：

Vue2 的 diff 算法主要通过同级节点之间的比较来进行。在对比新旧虚拟节点时，它采用“双端比较”的策略。首先分别比较新旧虚拟节点树的头部和尾部节点，通过四种可能的情况进行节点的移动、删除和创建。具体步骤如下：

- 如果新旧头部节点相同，将两个头部节点向后移动。
- 如果新旧尾部节点相同，将两个尾部节点向前移动。
- 如果旧头部节点和新尾部节点相同，将旧头部节点移动到尾部。
- 如果旧尾部节点和新头部节点相同，将旧尾部节点移动到头部。

如果以上四种情况都不满足，Vue2 会创建一个新的 key 到 index 的映射表，然后遍历新的子节点，查找旧节点中是否存在相同的 key。如果找到相同的 key，将旧节点移动到正确的位置。否则，创建一个新节点并插入到正确的位置。最后，删除旧节点中未匹配的节点。

2. Vue3 diff 算法：

Vue3 的 diff 算法在 Vue2 的基础上进行了优化。Vue3 利用了“静态节点和动态节点”的概念，通过对静态节点进行跳过，减少了不必要的比较。此外，Vue3 对于静态节点和动态节点的处理也进行了优化。在处理动态节点时，Vue3 使用了一个名为 lis（Longest Increasing Subsequence，最长递增子序列）的算法，通过查找最长递增子序列，找到需要移动的最少节点数量，从而减少节点移动操作，提高性能。

总结：Vue 的 diff 算法用于比较新旧虚拟节点树的差异，从而实现高效的 DOM 更新。Vue2 和 Vue3 的 diff 算法都基于 Snabbdom 库，采用双端比较策略。Vue3 在 Vue2 的基础上进行了优化，引入了静态节点和动态节点的概念，通过跳过静态节点的比较和使用 lis 算法减少节点移动操作，提高了性能。

尽管 Vue3 的 diff 算法相较于 Vue2 进行了优化，但在实际应用中，性能提升的程度还取决于组件的结构和数据变化。以下是一些建议，可以帮助我们在使用 Vue 时充分利用 diff 算法的优势：

1. 使用 key：为列表中的每个节点分配唯一的 key，可以帮助 diff 算法更快地找到相应的节点，从而提高性能。尽量避免使用不稳定的值（例如随机数或索引）作为 key。
2. 避免不必要的节点更新：尽量避免在没有实际更改的情况下触发组件的重新渲染。可以使用计算属性、watchers 和 Vue 的性能优化功能（如 shouldComponentUpdate 和 keep-alive）来减少不必要的渲染。
3. 合理划分组件：将大型组件拆分为更小的子组件，以便更好地控制组件的更新。当某个子组件的状态发生变化时，只需更新该子组件，而不会影响其他子组件。
4. 优化动态节点：在 Vue3 中，利用静态节点和动态节点的概念，确保动态节点的数量和位置合理。这可以帮助减少 diff 算法的计算量，提高性能。

通过了解 Vue2 和 Vue3 中的 diff 算法原理，并结合实际项目中的组件结构和数据变化情况，我们可以更好地利用 Vue 的性能优势，构建高效的前端应用。

这些区别使得 Vue 3 在许多方面都比 Vue 2 更加强大和灵活。然而，值得注意的是，Vue 3 的这些新特性和改进也意味着从 Vue 2 迁移到 Vue 3 可能需要一些代码重构。

12. **自定义 hook**

vue2 中使用 mixin，问题有两个，mixin 生命周期比组件优先执行，mixin 中的变量可能会被引入的组件覆盖；
组件中的变量来源不好查找，不利于阅读

13. **全局函数和变量**

vue2 将变量和函数挂载到 Vue.prototype 上，
vue3 将变量和函数挂载到 app.config.globalProperties 上，避免污染 Vue.prototype

## vue 响应式原理

Vue.js 的响应式原理是其核心特性之一，它允许开发者以声明式的方式将数据与视图绑定。Vue 的响应式系统主要依赖于两个关键技术：观察者模式（Observer Pattern）和依赖收集（Dependency Collection）。

以下是 Vue 响应式原理的详细介绍：

1. **数据劫持（Data Proxy）**：

   - Vue.js 通过使用 ES5 的 `Object.defineProperty` 函数，将数据对象的所有属性转换为 getter/setter。这样，每当属性被访问（getter 被调用）或属性值被修改（setter 被调用）时，Vue 都能够知道。

2. **依赖收集（Dependency Collection）**：

   - 当组件渲染时，它会访问响应式数据的属性，这时 Vue 会记录当前正在渲染的组件作为依赖。这个过程称为依赖收集。每个组件都会维护一个依赖项列表，这些依赖项是组件需要观察的响应式数据。

3. **观察者模式（Observer Pattern）**：

   - Vue 实现了一个观察者模式，其中每个组件实例都是一个观察者。当响应式数据发生变化时，Vue 会通知所有订阅了这些数据的组件，触发它们的更新。

4. **发布-订阅（Pub-Sub）**：

   - Vue 的响应式系统内部实现了一个发布-订阅模式。当响应式数据的 setter 被调用，即数据发生变化时，Vue 会遍历该数据的所有订阅者（即依赖于该数据的组件），并通知它们进行更新。

5. **批量异步更新（Async Queue）**：

   - Vue 在检测到数据变化时并不会立即更新 DOM，而是把这些变化放到一个异步队列中。在下一个事件循环（tick）中，Vue 会清空队列，并执行实际的 DOM 更新。这种机制可以避免不必要的重复渲染，提高性能。

6. **Vue 3 中的 Proxy**：
   - 在 Vue 3 中，响应式系统使用了 ES6 的 Proxy 替代了 Vue 2 中的 `Object.defineProperty`。Proxy 提供了更全面的拦截特性，允许 Vue 监听更多类型的数据变化，如数组索引和长度的变化，以及更复杂的对象属性变化。

通过这些机制，Vue 能够确保视图与数据保持同步。当数据发生变化时，Vue 会自动更新 DOM，而开发者无需手动操作 DOM。这种数据驱动的视图更新方式大大简化了复杂应用的开发。

## vue 的路由模式

## `hash`

优点：

- 路由切换不会向服务器发送请求
- 页面切换不会刷新页面，用户体验好

缺点：

- 有#号不美观

实现：通过监听`window.onhashchange`事件来实现前端路由。

触发：浏览器的前进后退；`location.hash`的改变；`a`标签的点击事件。

## `history`

优点：

- URL 更美观，没有#号
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

## 生命周期

```javascript
setup onBeforeMount onMounted onBeforeUpdate onUpdated onBeforeUnmount onUnmounted

onErrorCaptured onRenderTracked onRenderTriggered
```

## 父子组件传参

- Vue3

### defineProps

```javascript
// 子组件接收父组件传过来的值
const props = defineProps({
  title: {
    type: String,
    default: "默认标题",
  },
});
// 使用
props.title;
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
    title: {},
  },
  methods: {
    // 使用
    changeTitle() {
      this.title;
    },
  },
};
```

## 计算属性

```javascript
import { computed, ref } from "vue";
let firstName = ref("张");
let lastName = ref("三");
// 第一种 选项式写法
computed({
  get() {
    return firstName.value + "-" + lastName.value;
  },
  set(val) {
    const names = (val.split("-")[(firstName.value, lastName.value)] = names);
  },
});

// 第二种 函数式写法，只支持getter函数，不能修改name的值
const name = computed(() => firstName.value + "-" + lastName.value);
```

## watch 监听器

```javascript
import { watch, ref } from "vue";
let message = ref("hello");
watch(message, (newValue, oldValue) => {
  console.log(newValue, oldValue);
});
// 监听多个值
watch([message, other], (newValue, oldValue) => {
  console.log(newValue, oldValue); // [message的新值, other的新值], [message的旧值, other的旧值]
});
// 深度监听，如果是ref对象，需要加上deep:true，而reactive对象默认就是深度监听的
watch(message, (newValue, oldValue) => {}, { deep: true });
// 监听一个对象的某个属性，变成函数返回值
watch(
  () => message.value.name,
  (newValue, oldValue) => {}
);
// 立即执行
watch(message, (newValue, oldValue) => {}, { immediate: true });
// 调用时机，pre表示在组件更新之前同步触发回调，sync表示同步执行，Vue 进行任何更新之前触发，post表示在组件更新之后同步触发回调
watch(message, (newValue, oldValue) => {}, { flush: "pre" }); // 在依赖变更之后同步触发回调
```

## watchEffect 侦听器

```javascript
import { watchEffect, ref } from "vue";
let message = ref("hello");
// 监听message的变化，自动执行回调函数，不需要手动调用
watchEffect(() => {
  console.log(message.value);
});
// 侦听器回调中能访问被 Vue 更新之后的所属组件的 DOM
watchPosEffect(() => {});
// 停止监听器，同步创建监听器时，无需手动停止，异步创建时，需要手动停止
const stop = watchEffect(() => {});
stop();

// 开发环境上使用
// onTrack 将在响应属性或引用作为依赖项被跟踪时被调用。
// onTrigger 将在侦听器回调被依赖项的变更触发时被调用。
watchEffect(() => {}, {
  onTrack() {},
  onTrigger() {},
});
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

> 注意递归组件中的事件需要停止冒泡

## 动态组件

不同的组件挂到同一个挂载点

### 第一种方式

```javascript
<component :is=""></component>
const comps = shallowRef([
  {
  name: 'A组件',
  component: markRaw(AVue)
},
{
  name: 'B组件',
  component: markRaw(BVue)
}])
```

使用`markRaw`和`shallowRef`避免性能损失

### 第二种方式

```javascript
// vue2的用法，字符串的形式
export default {
  components: [A, B],
};
const comps = shallowRef([
  {
    name: "A组件",
    component: "AVue",
  },
  {
    name: "B组件",
    component: "BVue",
  },
]);
```

## 插槽

`#插槽名称`是`v-slot="插槽名称"`的简写

## 异步组件 Suspense

优势：减少首屏加载时间，可以分包加载

```javascript
// Sync.vue
import axios from "../server/axios";
const { data } = await axios.get("./data.json");
```

```javascript
// Skeleton.vue 骨架屏
```

```javascript
// App.vue
import Skeleton from './components/Skeleton.vue'
import { defineAsyncComponent } from 'vue'
const syncVue = defineAsyncComponent(() => import('./components/Sync.vue'))

<template>
  <div id="app">
    <Suspense>
      <!-- 异步组件 -->
      <template #default>
        <sync-vue />
      </template>
      <!-- 异步组件加载完成之前显示 -->
      <template #fallback>
        <Skeleton></Skeleton>
      </template>
    </Suspense>
  </div>
</template>
```

## 传送组件 Teleport

```javascript
// App.vue
// to 传送到哪个元素下，可以为class、id、标签名
// disabled 禁用传送
<Teleport to="body" :disabled="true">
<div class="modal">我是一个弹窗</div>
</Teleport>
```

## 缓存组件 Keep-Alive

常用于表单页面，保留用户填写的数据，或路由页面，保留用户的滚动位置

```javascript
// max: 最大缓存数量
<keep-alive :include=['A'] :exclude=['B'] :max="10">
   <A v-if></A>
   <B v-else></B>
</keep-alive>
```

keep-alive 中的组件生命周期增加了两个钩子函数：`activated`和`deactivated`
初次进入时： onMounted> onActivated
退出后触发 deactivated
再次进入：
只会触发 onActivated

## 动画组件 transition

### 过渡动画类名

```javascript
<transition class="fade" :duration="50"></transition>
/** duration 动画多长时间结束，可能会打断active中设置的动画时间 */
/** from、to都是表示一帧，active表示中间的过程 */
/** 默认类名前缀.v-enter */
<style>
.fade-enter-from
.fade-enter-active // 过渡曲线
.fade-enter-to
.fade-leave-from
.fade-leave-active
.fade-leave-to
</style>
```

### 过渡动画结合 animate.css

```javascript
<transition
  enter-active-class="animate__animate animate__fadeout"
  enter-from-class=""
></transition>
```

### 过渡动画生命周期

```javascript
<transition
@before-enter="enterFrom"
@enter="enterActive"
@after-enter="enterTo"
@enter-cancelled="enterCancelled" // 如果动画被打断，会触发这个事件
@before-leave="leaveFrom"
@leave="leaveActive"
@after-leave="leaveTo"
@leave-cancelled="leaveCancelled"
></transition>

const enterFrom = (el) => {
   // el是动画元素
}
const enterActive = (el, done) => {
   // el是动画元素，done是一个函数，调用它表示动画结束
   setTimeout(() => {
      done() // 如果不调用，不会触发after-enter事件
   }, 1000);
}
```

### 首次加载过渡动画

```javascript
<transition
  appear
  appear-from-class=""
  appear-active-class=""
  appear-to-class=""
></transition>
```

### 过渡列表 transition-group

```html
<!-- tag指定包裹元素的标签，不填的不会创建新标签 -->
<transition-group name="list" tag="ul">
  <!-- 需要key -->
  <li v-for="item in list" :key="item.id">{{item.name}}</li>
</transition-group>
```

列表支持`flip`动画

```javascript
<transition-group tag="div" class="box" move-class="move">
  <div class="item" v-for="item in list" :key="item">{{item.value}}</div>
</transition-group>

// 生成9*9的表格
const list = ref(new Array(81).fill(undefined).map((item, index) => ({ id: index, value: index%9 + 1 })))
const changeShuffle = () => {
   // lodash的shuffle方法，打乱数组
  list.value = shuffle(list.value);
}

<style>
.box {
  display: grid;
  grid-template-columns: repeat(9, 25px);
  grid-template-rows: repeat(9, 25px);
  .item {
    width: 100%;
    height: 100%;
    border: 1px solid #ccc;
    display: flex;
    justify-content: center;
    align-items: center;
  }
}
.move {
  transition: transform 1s;
}
</style>
```

## 依赖注入 provide 和 inject

```javascript
// 父组件
const name = ref("张三");
provide("name", name);

// 子组件、孙组件接收父组件传过来的值
const name = inject("name");

// 子组件是可以修改父组件的值的，通过readonly设置只读
// 父组件
provide("name", readonly());
```

源码通过原型链的方式实现`Object.create(provides)`

## v-model

```javascript
// 可写多个v-model
// 父组件
<Child v-model="data" v-model:textVal="text"></Child>

// 子组件
<div>{{modelValue}}</div>
<button @click="change">改变v-model的值</button>

defineProps({ modelValue: String, textVal: String }) // modelValue是默认的，textVal是自定义的
const emits = defineEmits(['update:modelValue', 'update:textVal'])
const change = () => {
   emits('update:modelValue', '新值'); // 父组件的data值会改变
   emits('update:textVal', '新值'); // 父组件的text值会改变
}
```

## defineEmits/defineProps/defineModel

1. 都是函数
2. 参数的意义：传入后，会提供恰当的类型推导

## 自定义 hook

函数中可引入生命周期钩子函数

### 全局函数和变量

```javascript
app.config.globalProperties.$env = "dev";
app.config.globalProperties.$filter = {
  format(val) {
    return "格式化" + val;
  },
};
```

```javascript
// 组件中使用
<template><div>{{$env}}<div></template>
<script setup>
import { getCurrentInstance } from 'vue';
getCurrentInstance().proxy.$env;
</script>
```

# 应用

## UI 组件的二次封装

- 属性事件
  ```javascript
  <el-input v-bind="$attrs"></el-input>
  ```
- 插槽
  ```html
  <el-input v-bind="$attrs">
    <template v-for="(value, name) in $slots" #[name]="scopedData">
      <slot :name="name" v-bind="scopedData"></slot>
    </template>
  </el-input>
  ```
- ref
  不推荐使用
  使用的话将子组件的所有方法和属性都暴露出来，这样父组件就可以调用子组件的方法和属性，这样就破坏了封装性，不推荐使用
  ```javascript
  // 子组件
  const ref = this.$refs.elInput;
  for (let key in ref) {
    if (typeof ref[key] === "function") {
      this[key] = ref[key].bind(ref);
    }
  }
  // 父组件
  <Child ref="child" />;
  this.$refs.child.focus();
  ```

## 页面未显示--排查步骤

1. 是否解析了 html 页面，是否加载了 css、js 等，如果没有，则是后端问题

2. 加载 css、js 是否 404，如果是，则是路径问题，修改`vue.config.js`中的`publicPath`

3. 组件未加载出来，检查是否路由路径错误，修改`router/index.js`中的`base`

# Vue2

## NextTick 原理

解答方式：

1. nextTick 是什么，如何使用
2. 作用、好处
3. 原理是什么
4. 内部实现

nextTick 是一个函数，接收回调函数作为参数，当 DOM 更新之后执行回调函数

它的作用是当响应式数据发生变化时，不会立即更新 DOM，而是将这些变化缓存起来，可以避免频繁的操作 DOM 而导致性能问题

原理是将回调函数推迟到下一个事件循环的末尾执行，从而保证在回调函数执行时，DOM 已经完成了更新。

内部实现是先将回调函数加入到回调队列中，然后用异步任务执行回调队列中的函数，异步任务的执行方式优先级分别为：微任务（Promise.then、MutationObserver）、宏任务（setImmediate、setTimeout）。

## 数据双向绑定原理

1. 本质：数据变化后，视图自动更新，视图变化后，数据自动更新
2. 通常是通过 v-model 实现的，用来简化数据和视图的同步

## v-modal 原理

1. 本质：v-model 是一个语法糖，可以简化数据和视图的同步
2. 用法：在表单元素中，v-model 简写了表单元素的 value 和 input 事件，在自定义组件中使用 v-model，默认是使用 value 和 input 事件，可以通过 model 选项修改：model: { prop: 'value', event: 'change' }

## 数据响应式原理

1. 本质：数据变化后，会自动重新运行依赖该数据的函数
2. 表象是数据变化后，页面数据自动更新
3. vue 响应式是通过观察者模式实现的，主要有三个概念，数据劫持、依赖收集、派发更新
4. 内部实现
   将 data 和 props 中的数据通过 Observer 转换成响应式对象，使具有 getter 和 setter，
   第一次 当组件执行到 mounted 阶段时，生成 watcher 实例，watcher 实例会调用 render 函数，
   当 render 函数执行时，生成 VNode 并且会调用变量的 getter，getter 中调用 dep.depend，会将 watcher 实例订阅到队列中，实现依赖收集。当数据变化时，会调用变量的 setter，setter 中调用 dep.notify 触发 watcher 的执行，但不会立马执行，而是加入到队列中，使用 nextTick 执行，执行时会重新执行 render 函数，生成新的 VNode，然后和旧的 VNode 进行比较，更新不同的地方，最后更新到页面上
   而在 render 过程中，会重新收集依赖，并在收集完成后，清空依赖队列

   Dep 是观察者模式

## 组件间的传值方式

### 父子组件传值

- props/$emit
- $parent/$children
- v-model

### 祖先与后代组件传值

- provide/inject

- $attrs/$listeners

$attrs用于传递父组件中未被识别为 prop 的特性，常用于跨越多层嵌套组件传递属性以及高阶组件。
$listeners 用于传递父组件中事件监听器，常用于跨越多层嵌套组件传递事件以及高阶组件。

```javascript
<grandchild-component v-bind="$attrs"></grandchild-component>
<grandchild-component v-on="$listeners"></grandchild-component>
```

### 非父子组件传值

- EventBus 事件总线

### 全部组件传值

- Vuex

## Vuex 相关

### Vuex 的核心概念

### action 有哪些缺陷

造成一定的性能开销和调试困难，使用不好的情况下还会出现代码冗余
