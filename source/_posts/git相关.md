---
title: git相关
date: 2024-01-28 10:37:25
tags:
- git
categories: 
- git
---

# 一台电脑配置多个git

参考链接：[一台电脑配置多个Git](https://blog.csdn.net/qq_39127371/article/details/126692664)

# clone或push 超时

### 背景：

家里的网访问不了github，所以开了代理，结果在`hexo deploy`时提示`Failed to connect to github.com port 443 after 21096 ms: Timed out` `FATAL { err: Error: Spawn failed`

### 参考链接：[解决 Github port 443 : Timed out](https://zhuanlan.zhihu.com/p/636418854)

### 问题原因：git 所设端口与系统代理不一致，需重新设置

### 步骤：

1. 查看系统代理的ip和端口号：设置 --> 网络和Internet --> 代理 --> 手动设置代理  中的地址和端口

2. 修改git的网络设置

    ```C#
    # 换成自己的ip和端口
    git config --global http.proxy http://127.0.0.1:7890 
    git config --global https.proxy http://127.0.0.1:7890
    ```

3. 重新尝试clone或push

### 后续：

```C#
# 取消代理
git config --global --unset http.proxy
git config --global --unset https.proxy

# 查看代理
git config --global --get http.proxy
git config --global --get https.proxy
```


# git区分文件大小写

背景：git默认是不区分文件大小写的，这会使改文件的大小写后git提交不上去，造成后续引用上的问题

解决：`git config core.ignorecase false`