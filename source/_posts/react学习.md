---
title: react学习
tags:
  - react
categories:
  - 前端
date: 2024-07-11 09:57:05
---

## 组件

### 特点

当组件最外层有多个 dom 元素时，可以使用三种做法

1. 最外层包裹一个 div 元素，缺点引入了多余的 dom 节点
2. 导入 react 组件 Fragment，使用 Fragment 包裹
3. 一个空的闭合元素包裹，相当于 Fragment，`<> ...dom </>`

### 基本写法

```javascript
function ListGroup() {
  const name = "list";
  return <h1 className="title">{name}</h1>;
}
export default ListGroup;
```

```javascript
import ListGroup from "./components/ListGroup";
function App() {
  return (
    <div>
      <ListGroup></ListGroup>
    </div>
  );
}
export default App;
```
