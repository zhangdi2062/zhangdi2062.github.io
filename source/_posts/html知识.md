---
title: html知识
tags:
  - html
categories:
  - 前端
date: 2024-07-11 13:51:23
---

## html 代码第一行的作用

### 作用

文档类型声明，用于告诉浏览器使用哪种版本解析和呈现网页内容

### 举例

`<!DOCTYPE html>`HTML5 标准，支持过时的元素和属性，但在 vscode 中会报红提示

`<!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "http://www.w3.org/TR/html4/loose.dtd">`
HTML4.01 标准，允许使用过时的 HTML 元素和属性，兼容性较好

### 不同文档类型声明的区别：

1. 兼容性：不同的文档类型声明支持不同的 HTML 和 XHTML 版本，并具有不同的向后兼容性。
2. 严格性：Strict 类型要求更严格的语法和结构，Transitional 类型则允许使用一些过时的元素和属性。
3. 用途：Frameset 类型用于包含框架的页面，而 Strict 和 Transitional 类型用于普通页面。
4. 语法：HTML 和 XHTML 文档类型声明的语法略有不同，XHTML 使用更严格的 XML 语法。

## meta 标签的作用

概念：文档级元数据元素，不显示在页面上，但对浏览器等很重要

### charset

`<meta charset="UTF-8">`指定 HTML 文档的字符编码

### name 属性值作为 key，content 属性值作为 value

1. author 页面作者或公司名称
2. description 对页面内容的描述
3. keywords 关键词，现代搜索引擎对该标签的重视程度有所下降
4. viewpoint 设置视口的宽度和缩放比例，以确保页面在不同设备上具有良好的响应性
5. robots 用于告诉搜索引擎爬虫（如 Googlebot、Bingbot）如何索引页面内容，对 SEO 和搜索引擎行为有直接影响
6. renderer 主要用于某些特定的中国浏览器，指定浏览器的渲染模式，例如使用 Webkit 内核或 Trident 内核

### http-equiv 属性值作为 key，content 属性值作为 value

1. refresh 设置页面自动刷新或重定向，content 为数值时表示自动刷新，单位秒；值为`0;url='http://'`时表示立即重定向
2. Content-Type 指定文档的 MIME 类型和字符编码。在 HTML5 中不再需要
3. X-UA-Compatible 用来做 IE 浏览器适配，开发者偏好（meta 元素）优先于 Web 服务器设置（HTTP 头）
4. Cache-Control、Pragma、Expires 禁止缓存，HTTP 请求头中相关指令，优先于 HTML 中的 <meta> 标签
