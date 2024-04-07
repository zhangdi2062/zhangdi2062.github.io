---
title: c语言转wasm
tags:
  - js
categories:
  - 前端
date: 2024-03-28 17:38:06
---

## C环境

### 安装gcc，windows环境下安装MinGW-w64

下载地址：[MinGW-w64](https://sourceforge.net/projects/mingw-w64/files/)

参考[教程](https://blog.csdn.net/kramer_1711/article/details/119416512)

配置环境变量 path 后打开终端输入 `gcc -v` 查看是否安装成功

### emcc指令 

emcc 是 Emscripten 的编译器，它可以将 C/C++ 代码编译成 WebAssembly 代码。

```js
git clone https://github.com/emscripten-core/emsdk
cd emsdk
emsdk install latest
emsdk activate latest
emsdk_env.bat
```
### c转wasm

```javascript
emcc sum.c -s WASM=1 -o sum.js
```

生成 sum.wasm 和 sum.js 文件

### 使用

```html
<script src="sum.js"></script>
<script src="sum.wasm"></script>
```

## python转c

1. 新建`.pyx`文件，如`sum.pyx`，编写python代码
2. 新建`setup.py`，编写如下代码
```python
from setuptools import setup
from Cython.Build import cythonize

setup(
    ext_modules = cythonize("sum.pyx")
)
```
3. 执行`python setup.py build_ext --inplace`，生成`sum.c`文件

## js使用python

```html
<script src="https://cdn.jsdelivr.net/pyodide/v0.18.1/full/pyodide.js"></script>
<script>
    async function main() {
        let pyodide = await loadPyodide({
          indexURL: "https://cdn.jsdelivr.net/pyodide/v0.18.1/full/"
        });
        console.log('Pyodide loaded successfully.');
        let result = pyodide.runPython(`
          print(123)
        `);
    }
    main();
</script>
```