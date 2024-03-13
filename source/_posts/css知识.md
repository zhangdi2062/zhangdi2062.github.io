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

## bem命名规则 -block__element--modify

```css
/* 块用-连接 */
.el-input{}
/* 元素用__连接 */
.el-input__inner{}
/* 修饰用--连接 */
.el-button--primary{}
```

### sass写法

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
    $selector:&;
    @at-root {
        #{$selector + $elem-sel + $el} {
            @content;
        }
    }
}

@mixin m($mod) {
    $selector:&;
    @at-root {
        #{$selector + $mod-sel + $mod} {
            @content;
        }

    }
}
```

在vite工程中全局引入
```javascript
export default defineConfig({
  plugins: [vue()],
  css: {
    preprocessorOptions: {
      scss: {
        additionalData: `@import "./src/index.scss";`
      }
    }
  }
})
```