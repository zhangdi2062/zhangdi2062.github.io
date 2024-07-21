---
title: git相关
date: 2024-01-28 10:37:25
tags:
  - git
categories:
  - git
---

# 一台电脑配置多个 git

参考链接：[一台电脑配置多个 Git](https://blog.csdn.net/qq_39127371/article/details/126692664)

# clone 或 push 超时

### 背景：

家里的网访问不了 github，所以开了代理，结果在`hexo deploy`时提示`Failed to connect to github.com port 443 after 21096 ms: Timed out` `FATAL { err: Error: Spawn failed`

### 参考链接：[解决 Github port 443 : Timed out](https://zhuanlan.zhihu.com/p/636418854)

### 问题原因：git 所设端口与系统代理不一致，需重新设置

### 步骤：

1. 查看系统代理的 ip 和端口号：设置 --> 网络和 Internet --> 代理 --> 手动设置代理 中的地址和端口

2. 修改 git 的网络设置

   ```C#
   # 换成自己的ip和端口
   git config --global http.proxy http://127.0.0.1:7890
   git config --global https.proxy http://127.0.0.1:7890
   ```

3. 重新尝试 clone 或 push

### 后续：

```C#
# 取消代理
git config --global --unset http.proxy
git config --global --unset https.proxy

# 查看代理
git config --global --get http.proxy
git config --global --get https.proxy
```

# git 区分文件大小写

背景：git 默认是不区分文件大小写的，这会使改文件的大小写后 git 提交不上去，造成后续引用上的问题

解决：`git config core.ignorecase false`

# git 提交规范

```
type: commit 的类型
feat: 新特性
fix: 修改问题
refactor: 代码重构
docs: 文档修改
style: 代码格式修改, 注意不是 css 修改
test: 测试用例修改
chore: 其他修改, 比如构建流程, 依赖管理.
scope: commit 影响的范围, 比如: route, component, utils, build...
subject: commit 的概述, 建议符合  50/72 formatting
body: commit 具体修改内容, 可以分为多行, 建议符合 50/72 formatting
footer: 一些备注, 通常是 BREAKING CHANGE 或修复的 bug 的链接.
```

# 一些报错

### unable to rewind rpc post data - try increasing http.postBuffer

增加缓存大小：
`git config http.postBuffer 524288000`

### Git: RPC failed; curl 56 OpenSSL SSL read: error:140943FC:SSLroutines:ssl3 read bytes:sslv3 alert bad record mac, errno 0

[参考链接](https://blog.csdn.net/qq_45644092/article/details/105625876)
