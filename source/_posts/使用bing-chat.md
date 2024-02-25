---
title: 使用bing chat
date: 2023-12-27 13:35:13
tags: 
- GPT
- bing
- 必应
categories:
- AI
---

# 使用必应聊天遇到的问题

## 1. 没有聊天选项

浏览器插件方式（需开启科学代理）

1. 安装Chrome Unlock New Bing AI扩展程序

在Chrome或其他Chromium内核浏览器中使用 New Bing AI

2. 采用全局代理，或者走给名单方式 代理所有Bing相关的请求： *://*.bing.com/*

3. 访问 bing.com/new

4. 将设置中的国家/地区改为代理地区

5. 访问 bing.com/new，出现聊天界面


## 2. 一直提示登录以继续你的聊天

1. 说明账号被封，需要使用Gmail邮箱重新注册一个账号，地点选节点所在地区

2. 将设置中的国家/地区改为代理地区

## 3. 设置里的国家/地区自动变为中国

1. 浏览器设置——隐私、搜索和服务——服务——地址栏和搜索——管理搜索引擎

2. 添加搜索引擎 

名称自定义

快捷方式是在地址栏快捷唤醒的值

以 %s 代替查询的 URL填入`{bing:baseURL}search?q=%s&{bing:cvid}{bing:msb}{google:assistedQueryStats}`

3. 将添加的搜索引擎设为默认值