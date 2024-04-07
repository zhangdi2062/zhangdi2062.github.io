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

# 新建博客时，信息头自定义

默认信息

```
---
title: {{ title }}
date: {{ date }}
tags:
---
```

修改`scaffolds\post.md`中的内容即可

# hexo博客中添加图片

安装`npm install https://github.com/CodeFalling/hexo-asset-image --save`

在`_config.yml`中添加或修改`post_asset_folder: true`

在博客中添加图片时，将图片放在博客同名的文件夹中，然后在博客中使用`![图片说明](图片名称.png)`

# 报错

1. 'hexo' 不是内部或外部命令，也不是可运行的程序或批处理文件。| hexo : 无法将“hexo”项识别为 cmdlet、函数、脚本文件或可运行程序的名称。

解决：删除package-lock.json，重新npm i;确认node版本是否有切换，切换回来