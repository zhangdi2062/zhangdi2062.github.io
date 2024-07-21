---
title: chrome调试工具devtools
tags:
  - devtools
categories:
  - 前端
date: 2024-04-16 11:49:00
---

# chrome调试工具
DevTools

## Elements

### 页面-右键-检查

- 定位到元素位置

### DOM Tree

- 方向 上下键

	- 元素选中移动

- 方向 左右键

	- 收起 展开

- dom右键

	- 添加编辑删除撤销

	- copy

		- 复制元素多种形态

	- Hide Element

		- 隐藏元素，visibility

		- H键隐藏/显示

	- Force state

		- 强制保持在某种状态

	- Break on

		- 子树更新时断点

		- 属性更新时断点

		- 移除元素时断点

	- Scroll into view

		- 将视图滚动到当前节点

	- Focus

		- 设置焦点

	- Badge Settings

		- 设置是否显示flex、grid等布局标志

	- Store as global variable

		- 保存为全局变量

- 暂停hover状态

	- 在Sources面板下，按F8或command+\，会执行断点再去选取

### 面板

- Styles

	- 过滤样式

	- 添加移除class

- Computed

	- 计算属性

- Layout

	- grid和flex的布局

- Event Listener

	- 该元素的事件

- DOM Breakpoints

	- 该元素的断点

### 截屏

- command/control + shift + P 输入Capture

## Console

### ESC调用或关闭console

### console方法

- clear

	- 清空控制台

- debug

	- 级别低，默认不显示

- table

	- 打印数组

- assert

	- 错的执行，不错不执行

- dir

	- 对象的JSON属性，例如dom、函数

- time/timeEnd

	- 计时

- group/groupEnd

	- 分组

- trace

	- 调用队列

### 内置方法

- $0 - $4

	- 前5个选中的dom

- $_

	- 上一个结果

- $

	- document.querySelector

- $$

	- document.querySelectorAll

- getEventListeners

	- 获取dom注册的事件

- copy(anything)

	- 拷贝anything到剪贴板上，并json格式化，再也不怕没有notepad++了

- debug(fn)/undebug(fn)

	- 相当于在函数的第一行打断点

### 功能

- 上下文

- 监听对象实时表达

	- 例如：监听层级、dom

- 筛选

	- 支持正则

- settings

	- Hide network 

		- 隐藏http请求

	- Preserve log

		- 页面重载时不清除打印

	- Selected context only

		- 只显示当前上下文中的信息

	- Group similar messages in console 

		- 将相同信息打组

	- Log XMLHttpRequests

		- 打印XMLHttpRequest和Fetch的请求结果

	- Eager evaluation

		- 立即求值

	- Autocomplete from history

		- 记录历史输入

	- Evaluate triggers user activation

		- 手动触发弹窗、自动播放、下载档案等

			- window.open()

## Sources

### 设置-Experiments-Auto pretty print

- 自动美化代码

### 文件导航

- Page

- Filesystem

	- 拖入一个文件夹，自动映射对应关系，同步更改

- Overrides

	- 本地覆盖

- Content scripts

	- 注入的代码，一般指扩展插件

- Snippets

	- 建立代码片段，可以将常用的代码保存起来

		- 命令行运行：command+O，输入!和片段的名字

### 代码编辑

- ctrl + P找文件

- 打断点

- Edit Breakpoint

	- 满足条件执行断点

### 代码调试

- Pause on exception

	- 在异常时中断，例如：console.error

- Threads

	- 主线程和其他上下文

- Watch

	- 监听变量，有时需要点击刷新

- Breakpoints

	- 断点列表，可一次性移除所有

- Scope

	- 作用域链

- Call Stack

	- 调用栈，查找问题来源很方便

- XHR/fetch Breakpoints

	- 网络断点

- DOM Breakpoints

	- dom断点

- Global Listeners

	- 全局监听断点

- Event Listener Breakpoints

	- 事件监听断点

- CSP violation Breakpoints

	- 启用受信任脚本类型，防止XSS攻击

## Network

### 停止记录网络请求

### 过滤

- Hide data URLs

	- 隐藏data:开头的

- 搜索框：is:

### 搜索

- 全局搜索，支持正则

### Preserve log

- 跨页面跳转，保留网络请求，例如登录页跳转首页

### Disable cache

- 禁用浏览器缓存

### Throttling

- 模拟网络环境

### 底部信息

### 小贴士

- 获取图片base64编码

	- img类型-preview-右键-copy  image as data url

## Performance

## Application

### storage

- 清空站点缓存

## More tools

### Rendering

- Paint flashing

	- 页面重绘

- FPS

	- 帧率

### Coverage

- 覆盖范围，监控没有使用到的脚本

### Animations

- 调整CSS动画

### Request blocking

- 阻止请求某些资源

### Search

- 所有资源全局搜索

## 文档地址https://developer.chrome.com/docs/devtools/

## 命令行command/control + shift + P

