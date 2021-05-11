[toc]

# docs MD编写规范

本文档主要描述md文档在sphinx环境下需要注意的事项



## 文档

文档目录：工蜂平台：ChainMaker/docs /docsify /docs

图片目录：所有图片放在docs /docsify /docs/images 文件夹下



## 编写格式规范

1.标题无须手动添加序号，直接在总索引文件index.rst中加入 :numbered:即可自动生成索引序号

样例：

```
.. toctree::
    :maxdepth: 2
    :caption: 快速入门
    :numbered:

    quickstart.rst
```

2.每个md文档必有有且只有一个一级标题

3.图片要用相对路径

样例：

```html
<img src="../images/add-sdk-jar.png" style="zoom:50%;" />
```

