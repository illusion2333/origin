[toc]





# readthecods docs MD编写规范

本文档主要描述MD文档在sphinx环境下需要注意的事项

地址： <a href="https://docs.chainmaker.org.cn" target="_blank">docs.chainmaker.org.cn</a>



## 文档

**文档目录**：工蜂平台：[ChainMaker/docs /readthedocs/docs](https://git.code.tencent.com/ChainMaker/docs/tree/readthedocs/readthedocs/docs)

**图片目录**：所有图片放在[docs /readthedocs /docs/images](https://git.code.tencent.com/ChainMaker/docs/tree/readthedocs/readthedocs/docs/images) 文件夹下



## 编写格式规范

0. **新加的文件需要添加到索引`index.rst`中**

1.  **标题无须手动添加序号**

   直接在总索引文件[docs /readthedocs /docs/index.rst](https://git.code.tencent.com/ChainMaker/docs/blob/readthedocs/readthedocs/docs/index.rst)中加入` :numbered: `即可自动生成索引序号

样例：

```rst
.. toctree::
    :maxdepth: 2
    :caption: 快速入门
    :numbered:

    tutorial/quick_start.md
```

2.  **每个MD文档有且仅有一个一级标题**

3. **图片要用相对路径（必须用英文路径和命名）**

   样例如下：

```markdown
![quick_start_add-sdk-jar.png](../images/quick_start_add-sdk-jar.png) 
```

4. 新添加的图片需要提交UI由UI重新做图
5. 可支持标准html标签，但标准html标签内写入markdown语法可能不会被识别。