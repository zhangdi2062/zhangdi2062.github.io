---
title: vite学习
tags:
  - js
categories:
  - 前端
date: 2024-03-03 21:45:00
---

## vite 特性

- 快速冷启动
- 模块热更新
- 按需编译，避免编译用不到的文件
- 开箱即用，避免各种loader和plugin的配置

## 核心功能

- 静态web服务器
- 编译单文件组件
 - 拦截浏览器不识别的模块，并处理
- HMR，通过WebSocket实现

## 创建项目

`npm install vite@latest`