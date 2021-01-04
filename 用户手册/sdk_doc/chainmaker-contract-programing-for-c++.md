

# ChainMaker Contract Programing for C++

[TOC]

读者对象：本文主要描述使用C++进行ChainMaker合约编写的方法，主要面向于使用C++进行ChainMaker的合约开发的开发者。

## 1 合约编写流程

## 1.1 使用IDE进行合约开发

请参考文档：[《ChainMaker IDE User Manual》](./chainmaker-ide-user-manual.md)

### 1.2 框架描述

使用IDE新建一个C++语言的合约项目之后，IDE会默认将C++ SDK和一些工具代码加到项目中去，如下图：

<img src="../images/cpp-frame.png" alt="cpp-frame.png" style="zoom:50%;" />

对IDE默认附带的框架文件描述如下：

c++:

- chainmaker
  - basic_iterator.cc：  迭代器实现
  - basic_iterator.h： 迭代器头文件声明
  - chainmaker.h： sdk主要接口头文件声明，详情见[SDK API描述](#api)
  - context_impl.cc：  与链交互接口实现
  - context_impl.h：  与链交互头文件声明
  - contract.cc： 合约基础工具类
  - error.h： 
  - exports.js：  编译合约导出函数
  - safemath.h 
  - syscall.cc： 与链交互入口
  - syscall.h：  与链交互头文件声明
- pb
  - contract.pb.cc：与链交互数据协议
  - contract.pb.h：与链交互数据协议头文件声明
- main.cc： 用户写合约入口，[如下](#fact)
- Makefile： 常用build命令

### 1.3 示例代码说明

**存证合约示例：main.cc<span id="fact"></span>** 实现功能

1、存储文件哈希和文件名称和该交易的ID。

2、通过文件哈希查询该条记录

```c++
#include "chainmaker/chainmaker.h"

using namespace chainmaker;

class Counter : public Contract {
public:
    void init_contract() {}
    void upgrade() {}
    // 保存
    void save() {
        // 获取SDK 接口上下文
        Context* ctx = context();
        // 定义变量
        std::string time;
        std::string file_hash;
        std::string file_name;
        std::string tx_id;
		    // 获取参数
        ctx->arg("time", time);
        ctx->arg("file_hash", file_hash);
        ctx->arg("file_name", file_name);
        ctx->arg("tx_id", tx_id);
		    // 存储数据
        ctx->put_object("fact"+ file_hash,  tx_id+" "+time+" "+file_hash+" "+file_name);
        // 记录日志
        ctx->log("call save() result:" + tx_id+" "+time+" "+file_hash+" "+file_name);
        // 返回结果
        ctx->success(tx_id+" "+time+" "+file_hash+" "+file_name);
    }

    // 查询
    void find_by_file_hash() {
        // 获取SDK 接口上下文
    	  Context* ctx = context();

		    // 获取参数
        std::string file_hash;
        ctx->arg("file_hash", file_hash);
		
        // 查询数据
    	  std::string value;
        ctx->get_object("fact"+ file_hash, &value);
        // 记录日志
        ctx->log("call find_by_file_hash()-" + file_hash + ",result:" + value);
        // 返回结果
        ctx->success(value);
    }

};

// 在创建本合约时, 调用一次init方法. ChainMaker不允许用户直接调用该方法.
WASM_EXPORT void init_contract() {
    Counter counter;
    counter.init_contract();
}

// 在升级本合约时, 对于每一个升级的版本调用一次upgrade方法. ChainMaker不允许用户直接调用该方法.
WASM_EXPORT void upgrade() {
    Counter counter;
    counter.init();
}

WASM_EXPORT void save() {
    Counter counter;
    counter.save();
}

WASM_EXPORT void find_by_file_hash() {
    Counter counter;
    counter.find_by_file_hash();
}
```



### 1.4 代码编写规则


**对链暴露方法写法为：**

- WASM_EXPORT： 必须，暴露声明
- void： 必须，无返回值
- method_name()： 必须，暴露方法名称

```c++
// 示例
WASM_EXPORT void init_contract() {
    
}
```

**其中init_contract、upgrade方法必须有且对外暴露**

- init_contract：创建合约会执行该方法
- upgrade： 升级合约会执行该方法

```rust

// 在创建本合约时, 调用一次init方法. ChainMaker不允许用户直接调用该方法.
WASM_EXPORT void init_contract() {
    // 安装时的业务逻辑，可为空
    
}

// 在升级本合约时, 对于每一个升级的版本调用一次upgrade方法. ChainMaker不允许用户直接调用该方法.
WASM_EXPORT void upgrade() {
    // 升级时的业务逻辑，可为空
    
}
```

**获取SDK 接口上下文**

```go
Context* ctx = context();
```


### 1.5 编译说明

在ChainMaker IDE中集成了编译器，可以对合约进行编译，集成的编译器是emcc 1.38.48版本，protobuf 使用3.7.1版本。用户如果手工编译需要先使用emcc 编译 protobuf ，编译之后执行emmake make即可。

## 2 合约发布过程

请参考：[《chainmaker-go-sdk.md》](./chainmaker-go-sdk.md)4.1.5 发送创建合约请求，或者[《chainmaker-java-sdk.md》](./chainmaker-java-sdk.md)2.1.4 创建合约。

## 3 合约调用过程

请参考：[《chainmaker-go-sdk.md》](./chainmaker-go-sdk.md)4.1.7 合约调用，或者[《chainmaker-java-sdk.md》](./chainmaker-java-sdk.md)2.1.7 执行合约。



## 4 C++ SDK API描述 <span id="api"></span>

### Arg

```c++
// 该接口可返回属性名为 “name” 的参数的属性值。
// @param name: 要获取值的参数名称
// @param value: 获取的参数值
// @return: 是否成功
bool arg(const std::string& name, std::string& value){}
```

###  get_object

```c++
// 获取key为"key"的值
// @param key: 获取对象的key
// @param value: 获取的对象值
// @return: 是否成功
bool get_object(const std::string& key, std::string* value){}
```

###  put_object

```c++
// 存储key为"key"的值
// @param key: 存储的对象key
// @param value: 存储的对象值
// @return: 是否成功
bool put_object(const std::string& key, const std::string& value){}
```

###  delete_object

```c++
// 删除key为"key"的值
// @param key: 删除的对象key
// @return: 是否成功
bool delete_object(const std::string& key) {}
```

### success

```c++
// 返回成功的结果
// @param body: 成功信息
void success(const std::string& body) {}
```

### error

```c++
// 返回失败结果
// @param body: 失败信息
void error(const std::string& body) {}
```

### call

```c++
// 调用合约
// @param contract: 合约名称
// @param method: 合约方法
// @param args: 调用合约的参数
// @param response: 调用合约的响应
// @return: 是否成功
bool call(const std::string& contract,
                      const std::string& method,
                      const std::map<std::string, std::string>& args,
                      Response* response){}
```

### log

```c++
// 输出日志事件
// @param body: 事件信息
void log(const std::string& body) {}
```