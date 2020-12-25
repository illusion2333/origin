# ChainMaker Contract Programing for C++

## 合约编写流程

## 合约编写示例

## 合约编译方法

C++ 版本的智能合约使用emcc 1.38.48版本编译器进行编译，并需要使用emcc 编译 protobuf ，protobuf 使用3.7.1版本，编译之后执行emmake make。

## 合约发布过程

## 合约调用过程



## C++ SDK API描述

```c++
// 该接口可返回属性名为 “name” 的参数的属性值。
bool arg(const std::string& name, std::string& value){}
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
```

## IDE工具介绍





