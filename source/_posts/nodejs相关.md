---
title: NodeJS相关
date: 2024-02-24 14:52:54
tags:
- nodejs
- nodejs安装
- nodejs管理
categories:
- 服务器
---

## nvm

### 安装

1. 下载`https://github.com/coreybutler/nvm-windows/releases` nvm-setup.zip

2. 解压安装

3. 找到安装路径下的settings.txt 文件，最后加入

```
node_mirror: https://npmmirror.com/mirrors/node/
npm_mirror: https://npmmirror.com/mirrors/npm/
```

### 常用命令

- 查看当前版本 `nvm version`

- 查看网络可以安装的版本 `nvm list available`

- 查看已安装版本 `nvm list`

- 安装指定版本：`nvm install 16`，

- 使用指定版本和位数：`nvm use [version] [arch]`，

- 打开nodejs版本控制 `nvm on`

- 关闭nodejs版本控制 `nvm off`

- 查看和设置代理 `nvm proxy [url]`

- 设置或查看node镜像 `nvm node_mirror [url]`

- 设置或查看npm镜像 `nvm npm_mirror [url]`

- 设置或查看root路径 `nvm root [path]`

- 查看当前系统的位数和nodejs的位数 `nvm arch`

- 卸载指定版本nodejs `nvm uninstall [version]`



## node

### 如何切换node版本

使用nvm(node version manager)，

### 使用哪个node版本

在工程根目录下创建 `.nvmrc` 文件，里面写上node版本，如`v18.19.1`，终端输入`nvm use`切换



## npm 

### 更新淘宝镜像

`npm config set registry https://registry.npmmirror.com`

### 切换镜像

`mmp use`

### 清空缓存

`npm cache clean --force`

### 创建用户

`npm adduser`

### 发布包

修改package.json中的version、main、files: []、module:"esm/index"
`npm login`
`npm publish`

## yarn 

### 更新淘宝镜像

`yarn config set registry https://registry.npmmirror.com`

### 报错

1. [!] Error: require() of ES Module xxx\node_modules\mime\dist\src\index.js from xxx\node_modules\rollup-plugin-serve\dist\index.cjs not supported.

粗暴解决：固定插件版本

```json
{
    "devDependencies": {},
    "resolutions": {
        "**/mime": "3.0.0"
    },
}
```

## pnpm

### 安装

`npm install -g pnpm`

### 获取仓库路径

`pnpm store path`

### 所有项目安装node_modules

根路径下新建`pnpm-workspace.yaml`

文件内容参考[pnpm-workspace.yaml](https://www.pnpm.cn/pnpm-workspace_yaml)

```yaml
packages:
  # all packages in direct subdirs of packages/
  - 'main'
  # all packages in subdirs of web/
  - 'web/**'
```

执行`pnpm install`

将公共依赖安装到根目录下，然后每个项目只安装自己的依赖

### 启动子应用

`pnpm -F react-demo dev`

### 应用安装公共依赖

`pnpm -F main add common`

## swc 替代ts-loader babel-loader