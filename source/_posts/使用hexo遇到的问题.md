---
title: 使用hexo遇到的问题
date: 2023-07-22 22:33:51
tags: 
- hexo
categories:
- 博客
---


# 多台电脑使用同一个hexo项目提交、更新

参考链接：[Hexo在多台电脑上提交和更新](https://blog.csdn.net/K1052176873/article/details/122879462)



# hexo next主题无法显示数学公式

参考链接：[hexo next主题解决无法显示数学公式](https://blog.csdn.net/weixin_44489823/article/details/105028860)

# hexo一篇博客添加多个标签

```
tags:
- js
- Array
```

# 没有标签和分类页

1. 执行下方命令在source目录下创建出categories目录和index.md文件
    ```
    hexo new page categories
    ```

2. 打开index.md文件，加入

    ```
    type: categories
    layout: "categories"
    ```

3. _config.yml中category_dir设置为categories，`category_dir: categories`