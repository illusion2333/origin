

# ChainMaker Contract Programing for C++

读者对象：本文主要描述使用C++进行ChainMaker合约编写的方法，主要面向于使用C++进行ChainMaker的合约开发的开发者。

## 1 合约编写流程

## 1.1 使用IDE进行合约开发

请参考文档：[《ChainMaker IDE User Manual》](./chainmaker-ide-user-manual.md)

### 1.2 框架描述

使用IDE新建一个C++语言的合约项目之后，IDE会默认将C++ SDK和一些工具代码加到项目中去，如下图：

![image-20201229102614590](/Users/tianlehan/Library/Application Support/typora-user-images/image-20201229102614590.png)

对IDE默认附带的框架文件描述如下：





### 1.3 编译说明

在《ChainMaker IDE User Manual》中集成了编译器，可以对合约进行编译，集成的编译器是emcc 1.38.48版本，protobuf 使用3.7.1版本。用户如果手工编译需要先使用emcc 编译 protobuf ，编译之后执行emmake make即可。

## 2 合约发布过程

简单的脚本测试，更详细的应用构建参考客户端SDK

## 3 合约调用过程

简单的脚本测试，更详细的应用构建参考客户端SDK



## 4 C++ SDK API描述

### Arg

```c++
// 该接口可返回属性名为 “name” 的参数的属性值。
bool arg(const std::string& name, std::string& value){}
```

输入说明

| 输入参数 | 说明                 |
| :------: | -------------------- |
|   name   | 需要查询的参数name值 |

输出说明

| 输出类型 | 说明                   |
| :------: | ---------------------- |
|  string  | 根据name查询到的参数值 |

###  get_object



// 获取key为"key"的值
bool get_object(const std::string& key, std::string* value){}
// 存储key为"key"的值
bool put_object(const std::string& key, const std::string& value){}
// 删除key为"key"的值
bool delete_object(const std::string& key) {}
// 返回成功的结果
void success(const std::string& body) {}
// 返回失败结果
void error(const std::string& body) {}
// 调用合约
bool call(const std::string& contract,
                      const std::string& method,
                      const std::map<std::string, std::string>& args,
                      Response* response){}
// 输出日志事件
void log(const std::string& body) {}