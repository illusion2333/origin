

# ChainMaker Contract Programing for C++

读者对象：本文主要描述使用C++进行ChainMaker合约编写的方法，主要面向于使用C++进行ChainMaker的合约开发的开发者。

## 1 合约编写流程

## 1.1 使用IDE进行合约开发

请参考文档：[《ChainMaker IDE User Manual》](./chainmaker-ide-user-manual.md)

### 1.2 框架描述

使用IDE新建一个C++语言的合约项目之后，IDE会默认将C++ SDK和一些工具代码加到项目中去，如下图：

<img src="../images/cpp-frame.png" alt="cpp-frame.png" style="zoom:50%;" />

对IDE默认附带的框架文件描述如下：

### 1.3 示例代码说明

### 1.4 代码编写规则

### 1.5 编译说明

在《ChainMaker IDE User Manual》中集成了编译器，可以对合约进行编译，集成的编译器是emcc 1.38.48版本，protobuf 使用3.7.1版本。用户如果手工编译需要先使用emcc 编译 protobuf ，编译之后执行emmake make即可。

## 2 合约发布过程

请参考：[《chainmaker-go-sdk.md》](./chainmaker-go-sdk.md)4.1.5 发送创建合约请求，或者[《chainmaker-java-sdk.md》](./chainmaker-java-sdk.md)2.1.4 创建合约。

## 3 合约调用过程

请参考：[《chainmaker-go-sdk.md》](./chainmaker-go-sdk.md)4.1.7 合约调用，或者[《chainmaker-java-sdk.md》](./chainmaker-java-sdk.md)2.1.7 执行合约。



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

```c++
// 获取key为"key"的值
bool get_object(const std::string& key, std::string* value){}
```

输入说明

| 输入参数 | 说明            |
| :------: | --------------- |
|   key    | 需要查询的key值 |

输出说明

| 输出类型 | 说明                  |
| :------: | --------------------- |
|  string  | 根据key查询到的结果值 |

###  put_object

```c++
// 存储key为"key"的值
bool put_object(const std::string& key, const std::string& value){}
```

输入说明

| 输入参数 | 说明            |
| :------: | --------------- |
|   key    | 需要存储的key值 |

输出说明

| 输出类型 | 说明             |
| :------: | ---------------- |
|  string  | 需要存储的对象值 |

###  delete_object

```c++
// 删除key为"key"的值
bool delete_object(const std::string& key) {}
```

输入说明

| 输入参数 | 说明                |
| :------: | ------------------- |
|   key    | 需要删除的对象key值 |

### success

```c++
// 返回成功的结果
void success(const std::string& body) {}
```

输入说明

| 输入参数 | 说明           |
| :------: | -------------- |
|   body   | 返回的成功信息 |

### error

```c++
// 返回失败结果
void error(const std::string& body) {}
```

输入说明

| 输入参数 | 说明           |
| :------: | -------------- |
|   body   | 返回的失败信息 |

### call

```c++
// 调用合约
bool call(const std::string& contract,
                      const std::string& method,
                      const std::map<std::string, std::string>& args,
                      Response* response){}
```

输入说明

| 输入参数 | 说明           |
| :------: | -------------- |
| contract | 调用合约的名称 |
|  method  | 调用合约的方法 |
|   args   | 调用合约的参数 |
| response | 调用合约的响应 |

输出说明

| 输出类型 | 说明         |
| :------: | ------------ |
|   bool   | 调用是否成功 |

### log

```c++
// 输出日志事件
void log(const std::string& body) {}
```

输入说明

| 输入参数 | 说明           |
| :------: | -------------- |
|   body   | 记录的事件信息 |

### 