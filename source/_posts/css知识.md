---
title: css知识
tags:
  - css
categories:
  - 前端
date: 2024-03-03 21:21:31
---

## prefetch 和 preload

### prefetch

用户未来的浏览有可能需要加载目标资源，所以浏览器有可能通过事先获取和缓存对应资源，优化用户体验。

### preload

指定页面很快就需要的资源，这些资源是你希望在页面生命周期的早期就开始加载的，早于浏览器的主要渲染机制启动。这可以确保它们更早可用，并且不太可能阻塞页面的渲染，从而提高性能。

尽管名称中包含“load”一词，但它并不加载和执行脚本，而只是安排脚本以更高的优先级进行下载和缓存。

使用`link` + `preload` 可以加载多种类型的内容，音视频、图片、字体等等

```html
<link rel="preload" href="style.css" as="style" />
<link rel="preload" href="main.js" as="script" />
<!-- as 属性中的资源类型 -->
```

## bem 命名规则 -block\_\_element--modify

```css
/* 块用-连接 */
.el-input {
}
/* 元素用__连接 */
.el-input__inner {
}
/* 修饰用--连接 */
.el-button--primary {
}
```

### sass 写法

```scss
$namespace: "hh" !default;
$block-sel: "-" !default;
$elem-sel: "__" !default;
$mod-sel: "--" !default;

@mixin b($block) {
  @at-root {
    .#{$namespace + $block-sel + $block} {
      @content;
    }
  }
}

@mixin e($el) {
  $selector: &;
  @at-root {
    #{$selector + $elem-sel + $el} {
      @content;
    }
  }
}

@mixin m($mod) {
  $selector: &;
  @at-root {
    #{$selector + $mod-sel + $mod} {
      @content;
    }
  }
}
```

在 vite 工程中全局引入

```javascript
export default defineConfig({
  plugins: [vue()],
  css: {
    preprocessorOptions: {
      scss: {
        additionalData: `@import "./src/index.scss";`,
      },
    },
  },
});
```

## 动画库

- [animate.css](https://animate.style/)
- [tweenmax](https://greensock.com/tweenmax/)
- [velocity](https://velocityjs.org/)
- [animejs](https://animejs.com/)
- [gsap](https://greensock.com/gsap/)
- [movejs](https://visionmedia.github.io/move.js/)

## content-box 和 border-box

content-box 是默认的盒子模型，元素的宽度和高度等于内容的宽度和高度。width+padding+border+margin

border-box 是 IE 盒模型，元素的宽度和高度等于内容、内边距和边框的宽度和高度之和。
width(width+padding+border)+margin

## 尺寸的百分比

1. 普通元素的参考系是**父元素的内容区域**
2. 绝对（固定）定位元素的参考系为**父元素中第一个定位元素的 padding 区域**

## BFC

## 理解 z-index 生效顺序

首先理解 3 个名词

1. 层叠上下文
2. 层叠等级
3. 层叠顺序

### 层叠上下文

1. 根元素`<html></html>`本身就具有层叠上下文，称为“根层叠上下文”
2. 普通元素设置 position 属性为非 static 值并设置 z-index 属性为具体数值，产生层叠上下文
3. 一些属性设置了也会产生层叠上下文

- 父元素的 display 属性值为 flex|inline-flex|grid，子元素 z-index 属性值不为 auto 的时候，子元素为层叠上下文元素；
- 元素的 opacity 属性值不是 1；
- CSS3 属性 transform、filter、perspective、clip-path、mask 设定值时
- 元素 mix-blend-mode 属性值不是 normal`；
- 元素的 isolation 属性值是 isolate；
- will-change 指定的属性值为上面任意一个；
- contain 属性值为 layout、paint 或包含它们其中之一的合成值（比如 contain: strict、contain: content）的元素

同层的层叠上下文相互比较 z-index，例如下方，d1 和 d2-1 都是除 html 外最外层的层叠上下文(d2 不产生层叠上下文)，所以它俩相互比较，d1 显示在 d2-1 上方

```html
<html>
  <head>
    <style>
      .d1 {
        width: 300px;
        height: 100px;
        background: red;
        position: absolute;
        z-index: 2;
      }
      .d2-1 {
        position: absolute;
        width: 100px;
        height: 200px;
        background: pink;
        z-index: 1;
      }
    </style>
  </head>
  <body>
    <div class="d1"></div>
    <div class="d2">
      <div class="d2-1"></div>
    </div>
  </body>
</html>
```

### 层叠等级

层叠等级的比较只有在当前层叠上下文元素中才有意义，比如下方，首先比较同层的层叠上下文，d1 在上，d1-1 为子元素遵从父元素，绘制在父元素上方，d2 在下，d2-1 层叠等级大于 d2-2 在上方，整体顺序为 d2 < d2-2 < d2-1 < d1 < d1-1

```html
<html>
  <head>
    <style>
      .d1 {
        width: 300px;
        height: 100px;
        background: red;
        position: absolute;
        z-index: 2;
      }
      .d1-1 {
        position: absolute;
        width: 100px;
        height: 100px;
        background: blue;
        z-index: -1;
      }
      .d2 {
        width: 100px;
        height: 300px;
        background: yellow;
        position: absolute;
        z-index: 1;
      }
      .d2-1 {
        position: absolute;
        width: 100px;
        height: 200px;
        background: pink;
        z-index: 10;
      }
      .d2-2 {
        position: absolute;
        width: 100px;
        height: 250px;
        background: green;
        z-index: 9;
      }
    </style>
  </head>
  <body>
    <div class="d1">
      <div class="d1-1"></div>
    </div>
    <div class="d2">
      <div class="d2-1"></div>
      <div class="d2-2"></div>
    </div>
  </body>
</html>
```

### 层叠顺序

1. z-index < 0 的元素
2. 未指定 z-index 的元素 或者 z-index: auto 或者 z-index: 0
3. z-index > 0 的元素

## flex

### 容器属性

- flex-direction:主轴方向，默认 row，水平从左到右
- flex-wrap: 是否可换行，默认 nowrap
- flex-flow: direction 和 wrap 的简写
- justify-content: 项目在主轴上的对齐方式，默认 flex-start
- align-items: 项目中交叉轴上的对齐方式，默认 stretch
- align-content: 多行交叉轴的对齐方式，单行时设置无效，默认 stretch。多行时 align-items 无效
- gap: 主轴和交叉轴间距

### 项目属性

- order: 项目的排列顺序，默认为 0
- flex-grow: 在剩余空间上的放大倍数，默认为 0
- flex-shrink: 在剩余空间上的缩小倍数，默认为 1
- flex-basic: 项目在主轴方向上的初始大小
- flex: 上面三个属性的简写
- align-self: 自己的对齐方式

## grid

### 容器属性

- grid-template-columns: 网格的列
- grid-template-rows: 网格的行
- grid-template-areas: 网格命名

```css
.container {
  grid-template-columns: repeat(2, 100px 1fr 1fr);
  grid-template-rows: repeat(auto-fill, 100px);
  /* grid-template-columns: 1fr 1fr minmax(100px, 1fr);表示列宽不小于100px，不大于1fr */
  /* grid-template-columns: 100px auto 100px;auto关键字表示由浏览器自己决定长度 */
}
```

### 项目属性

## 移动端适配

### 百分比

缺点：

1. 不是所有情况都根据父级宽度计算，还可能根据父级高度或自身宽高计算
2. 容易出现嵌套层数太深的问题

### 视口单位 (Viewport Units - vw, vh)

原理：通过缩放 viewPoint 使页面正常显示。计算缩放比的公式为：设备宽度 / 布局宽度，设备宽度获取 clientWidth，布局宽度为设计图宽度

缺点：

1. 所有设备展示一样，不具备媒体查询的效果
2. 兼容性问题，旧版浏览器可能不支持

### DPR (Device Pixel Ratio)设备像素比率

像素比(物理像素 / CSS 像素)

### rem

含义是相对于根元素的字体大小的单位
