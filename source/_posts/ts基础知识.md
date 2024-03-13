---
title: ts基础知识
date: 2024-01-28 11:44:41
tags:
- ts
categories:
- 前端
---

# 安装

`npm install -g typescript`

# 新建

`hello.ts`

# 编译

`tsc hello.ts`

## 报错不编译

`tsc --noEmitOnError hello.ts`    

## 编译成es5的版本(默认是es3)

`tsc --target es2015 hello.ts`

# 初始化&运行ts项目

`tsc init`

`npx tsc`

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

# 协变和逆变

只需保证 **__类型安全：所有成员可用__**

```ts
interface Fans {
    call():void
}

interface IKun extends Fans {
    sing(): void,
    dance(): void,
    basketball(): void,
}

interface SuperIKun extends IKun {
    rap(): void
}

const superIKun: SuperIKun = {
    call(){},
    sing(){},
    dance(){},
    basketball(){},
    rap(){}
}

const ikun: IKun = superIKun;
// 类型安全，所有成员可用
ikun.call();

const fans: Fans = {
    call(){}
} 

// 报错
// const kun:IKun = fans

// 协变：子类型给父类型，因为子类型中的属性一定是包含父类型的，所以类型一定安全
```

# 实现optional

```ts
type Article = {
    title: String,
    date: Date,
    author: String,
    readCount: Number
}
// 需求是实现文章的保存，date、readCount等字段如果没传则使用默认值
// 如果再创建下方的类型，会有代码重复、后期修改麻烦的问题
// type CreateArticle = {
//     title: String,
//     date?: Date,
//     author?: String,
//     readCount?: Number
// }

// 创建可选类型
// Omit T中排除掉K类型
// Pick T中获取K类型
// Partial将值变为可选的
type Optional<T, K extends keyof T> = Omit<T, K> & Partial<Pick<T, K>>;

type CreateArticleOptions = Optional<Article, 'author' | 'date' | 'readCount'>;

function createArticle(options: CreateArticleOptions) {}
```

# 泛型

动态类型，在定义函数时不明确类型，在调用时才明确

```javascript
function fn<T>(a: T, b: T):Array<T> {
  return [a, b];
}

fn(1, 2);
fn('x', 'y')
```

# 定义默认值 withDefaults

```ts
withDefaults(defineProps<{
  title: String,
  arr:Number[],
}>(), {
  arr: () => [666]
})
```

# 函数重载

类似`new Date()`会提示不同的参数类型
而普通函数如果可以接收不同的参数，可以用ts的函数重载来实现
```ts
function message(option: Object): void;
function message(text: string): void;
function message(text: string, duration: number): void;

function message(
    param1: String | Object,
    param2?: number
    ): void {}

message({
    mode: 'mode',
    duration: 3000
})

message('text');
message('text', 3000);
```
