# ChainMaker Contract Programing for C++

[TOC]

读者对象：本文主要描述使用C++进行ChainMaker合约编写的方法，主要面向于使用C++进行ChainMaker的合约开发的开发者。

## 1 合约编写流程

## 1.1 使用Docker镜像进行合约开发

ChainMaker官方已经将容器发布至GitHub

拉取镜像
```
docker pull huzhenyuan/chainmaker-cpp-contract:1.0.0
```

请指定你本机的工作目录$WORK_DIR，例如C:\tmp，挂载到docker容器中以方便后续进行必要的一些文件拷贝

```
docker run -it --name chainmaker-cpp-contract -v $WORK_DIR:/home huzhenyuan/chainmaker-cpp-contract:1.0.0 bash
```

编译合约

```
# cd /home/
# tar xvf /data/contract_cpp_template.tar.gz
# cd contract_cpp
# emmake make
```

生成合约的字节码文件在

```
/home/contract_cpp/main.wasm
```

通过本地模拟环境运行合约(首次编译运行合约可能需要10秒左右，下面以两数相除作为示例)

```
# wxvm main.wasm divide num1 100 num2 8

2021-03-09 03:36:46.864 [INFO]  [Vm] @chain01   wxvm/code_manager.go:81 compile wxvm code for contract test-contract,  time cost 4.6587203s
2021-03-09 03:36:46.926 [DEBUG] [Vm]    wxvm/context_service.go:206     wxvm log >>[test-TxId] [1] call divide()
2021-03-09 03:36:46.939 [DEBUG] [Vm]    wxvm/context_service.go:206     wxvm log >>[test-TxId] [1] num 1:100
2021-03-09 03:36:46.940 [DEBUG] [Vm]    wxvm/context_service.go:206     wxvm log >>[test-TxId] [1] num 2:8
2021-03-09 03:36:46.941 [DEBUG] [Vm]    wxvm/context_service.go:206     wxvm log >>[test-TxId] [1] divide result is 12
2021-03-09 03:36:46.942 [INFO]  [Vm] @chain01   main/main.go:29 contractResult :result:"divide result is 12"
```

其中除法的合约方法定义为：

```
#include "chainmaker/chainmaker.h"

using namespace chainmaker;

class Counter : public Contract {
public:
	...
	...
    void divide() {
        Context* ctx = context();
        ctx->log("call divide()");

        std::string num1_string;
        std::string num2_string;

        ctx->arg("num1", num1_string);
        ctx->arg("num2", num2_string);

        int num1 = atoi(num1_string.c_str());
        int num2 = atoi(num2_string.c_str());
        ctx->log("num 1:" + num1_string);
        ctx->log("num 2:" + num2_string);
        char buf[32];
        snprintf(buf, 32, "divide result is %d", (int) (num1 / num2));
        ctx->log(buf);

        ctx->success(buf);
    }
    ...
    ...
}

WASM_EXPORT void divide() {
    Counter counter;
    counter.divide();
}
```



### 1.2 框架描述

解压缩contract_cpp_template.tar.gz后，文件描述如下：

- chainmaker
  - basic_iterator.cc：  迭代器实现
  - basic_iterator.h： 迭代器头文件声明
  - chainmaker.h： sdk主要接口头文件声明，详情见[SDK API描述](#api)
  - context_impl.cc：  与链交互接口实现
  - context_impl.h：  与链交互头文件声明
  - contract.cc： 合约基础工具类
  - error.h： 异常处理类
  - exports.js：  编译合约导出函数
  - safemath.h：  assert异常处理
  - syscall.cc： 与链交互入口
  - syscall.h：  与链交互头文件声明
- pb
  - contract.pb.cc：与链交互数据协议
  - contract.pb.h：与链交互数据协议头文件声明
- main.cc： 用户写合约入口，[如下](#fact)
- Makefile： 常用build命令

### 1.3 示例代码说明

存证合约示例：main.cc，实现功能：

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
    counter.upgrade();
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

在ChainMaker提供的Docker容器中中集成了编译器，可以对合约进行编译，集成的编译器是emcc 1.38.48版本，protobuf 使用3.7.1版本。用户如果手工编译需要先使用emcc 编译 protobuf ，编译之后执行emmake make即可。

## 2 合约发布过程

请参考：[《chainmaker-go-sdk》](./chainmaker-go-sdk.md)发送创建合约请求的部分，或者[《chainmaker-java-sdk》](./chainmaker-java-sdk.md)创建合约的部分。

## 3 合约调用过程

请参考：[《chainmaker-go-sdk》](./chainmaker-go-sdk.md)合约调用的部分，或者[《chainmaker-java-sdk》](./chainmaker-java-sdk.md)执行合约的部分。



## 4 C++ SDK API描述 <span id="api"></span>

### arg

```c++
// 该接口可返回属性名为 “name” 的参数的属性值。
// @param name: 要获取值的参数名称
// @param value: 获取的参数值
// @return: 是否成功
bool arg(const std::string& name, std::string& value){}
```

需要注意的是通过arg接口返回的参数，全都都是字符串，合约开发者有必要将其他数据类型的参数与字符串做转换，包括atoi、itoa、自定义序列化方式等。

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
// @param key: 存储的对象key，注意key长度不允许超过64，且只允许大小写字母、数字、下划线、减号、小数点符号
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
// 跨合约调用
// @param contract: 合约名称
// @param method: 合约方法
// @param args: 调用合约的参数，调用参数仅仅接受字符串类型的key、value
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

