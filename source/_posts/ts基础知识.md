---
title: ts基础知识
date: 2024-01-28 11:44:41
tags:
- ts
categories:
- ts
---

# 安装

`npm install -g typescript`

# 新建

`hello.ts`

# 编译

`tsc hello.ts`

# 报错不编译

`tsc --noEmitOnError hello.ts`

# 编译成es5的版本(默认是es3)

`tsc --target es2015 hello.ts`

# 联合类型

表示值可能是这些类型中的任意一个

# 类型别名type

```ts
type ID = number | string;
```

```ts
type Point {
    x: number;
    y: number;
}
function printCoord(pt: Point) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
 
printCoord({ x: 100, y: 100 });
```

# 接口interface

```ts
interface Point {
    x: number;
    y: number;
}
function printCoord(pt: Point) {
  console.log("The coordinate's x value is " + pt.x);
  console.log("The coordinate's y value is " + pt.y);
}
 
printCoord({ x: 100, y: 100 });
```

# 类型和接口的相同和不同

两者用法上基本相同，最关键的差别在于类型别名本身无法添加新的属性，而接口是可以扩展的。

```ts
// Interface
// 通过继承扩展类型
interface Animal {
  name: string
}

interface Bear extends Animal {
  honey: boolean
}

const bear = getBear() 
bear.name
bear.honey
        
// Type
// 通过交集扩展类型
type Animal = {
  name: string
}

type Bear = Animal & { 
  honey: boolean 
}

const bear = getBear();
bear.name;
bear.honey;

```

```ts
// Interface
// 对一个已经存在的接口添加新的字段
interface Window {
  title: string
}

interface Window {
  ts: TypeScriptAPI
}

const src = 'const a = "Hello World"';
window.ts.transpileModule(src, {});
        
// Type
// 创建后不能被改变
type Window = {
  title: string
}

type Window = {
  ts: TypeScriptAPI
}

// Error: Duplicate identifier 'Window'.

```

# 类型断言

使用 `as` 或者 尖括号 语法，强制类型转换会报错，可以双重断言

# 字面量类型

常结合联合类型使用

```ts
function printText(s: string, alignment: "left" | "right" | "center") {
  // ...
}
```

```ts
interface Options {
  width: number;
}
function configure(x: Options | "auto") {
  // ...
}
```

# 非空断言操作符（后缀 !）

只有明确的知道这个值不可能是 null 或者 undefined 时才使用