# ChainMaker Contract Programing for go

读者对象：本文主要描述使用C++进行ChainMaker合约编写的方法，主要面向于使用C++进行ChainMaker的合约开发的开发者。

## 1 合约编写流程

## 1.1 使用IDE进行合约开发

请参考文档：[《ChainMaker IDE User Manual》](./chainmaker-ide-user-manual.md)

### 1.2 框架描述

使用IDE新建一个Go语言的项目之后，IDE会默认将Go SDK和一些工具代码加到项目中去，如下图：

<img src="/Users/tianlehan/Library/Application Support/typora-user-images/image-20201229101858097.png" alt="image-20201229101858097" style="zoom:50%;" />

对IDE默认附带的框架文件描述如下：

chainmaker.go：主要的Go SDK文件，详细接口说明见第4小节



### 1.3 编译说明

在《ChainMaker IDE User Manual》中集成了编译器，可以对合约进行编译。集成的编译器是 TinyGo。用户如果手工编译，需要将 SDK 和用户编写的智能合约放入同一个文件夹，并在此文件夹的当前路径执行如下编译命令：

```shell
tinygo build -no-debug -opt=s -o name.wasm -target wasm
```

命令中 “name.wasm” 为生成的WASM 字节码的文件名，由用户自行指定。

## 2 合约发布过程

请参考：[《chainmaker-go-sdk.md》](./chainmaker-go-sdk.md)4.1.5 发送创建合约请求，或者[《chainmaker-java-sdk.md》](./chainmaker-java-sdk.md)2.1.4 创建合约。

## 3 合约调用过程

请参考：[《chainmaker-go-sdk.md》](./chainmaker-go-sdk.md)4.1.7 合约调用，或者[《chainmaker-java-sdk.md》](./chainmaker-java-sdk.md)2.1.7 执行合约。



## 4 Go SDK API描述

### GetState

```  go
// 获取合约账户信息。该接口可从链上获取类别 “key” 下属性名为 “name” 的状态信息。
func GetState(key string, field string) (string, ResultCode) {} 
```

输入说明

| 输入参数 | 说明                       |
| :------: | :------------------------- |
|   key    | 需要查询的key值            |
|  field   | key值下属性名为field的属性 |

输出说明

|  输出类型  | 说明                       |
| :--------: | -------------------------- |
|   string   | 根据key和name查询到的值    |
| ResultCode | 0表示操作成，1表示操作失败 |

### GetStateFromKey

```go
// 获取合约账户信息。该接口可以从链上获取类别为key的状态信息
func GetStateFromKey(key string) (string, ResultCode) {}
```

输入说明

| 输入参数 | 说明            |
| :------: | :-------------- |
|   key    | 需要查询的key值 |

输出说明

| 输出参数 | 说明     |
| :------: | -------- |
|    1     | 更新失败 |
|    0     | 更新成功 |

### PutState

```go
// 写入合约账户信息。该接口可把类别 “key” 下属性名为 “name” 的状态更新到链上。更新成功返回0，失败则返回1。
func PutState(key string, field string, value string) ResultCode {}
```

输入说明

| 输入参数 | 说明                         |
| :------: | ---------------------------- |
|   key    | 需要存入的key值              |
|  field   | key值对应的field属性         |
|  value   | key值和name属性对应的value值 |

输出说明

| 输出参数 | 说明     |
| :------: | -------- |
|    1     | 更新失败 |
|    0     | 更新成功 |

### PutStateFromKey

```go
// 写入合约账户信息。
func PutStateFromKey(key string, value string) ResultCode
```

输入说明

| 输入参数 | 说明              |
| :------: | :---------------- |
|   key    | 需要写入值的key值 |

输出说明

| 输出参数 | 说明     |
| :------: | -------- |
|    1     | 更新失败 |
|    0     | 更新成功 |

### DeleteState

```go
// 删除合约账户信息。该接口可把类别 “key” 下属性名为 “name” 的状态从链上删除。
func DeleteState(key string, field string) ResultCode {}
```

输入说明

| 输入参数 | 说明                       |
| :------: | -------------------------- |
|   key    | 需要查询的key值            |
|  field   | key值下属性名为field的属性 |

输出说明

| 输出参数 | 说明     |
| :------: | -------- |
|    1     | 更新失败 |
|    0     | 更新成功 |

### getArgsMap()

```go
// 该接口可把 json 格式的数据反序列化，并将解析出的数据存于一个 map[string]interface{} 类型的全局变量。
func getArgsMap() error {} 
```

输出说明

| 输入参数 | 说明         |
| :------: | ------------ |
|   nil    | 数据解析失败 |

### Args

```go
// 该接口调用 getArgsMap() 接口，把 json 格式的数据反序列化，并将解析出的数据返还给用户。
func Args() map[string]interface{} {}  
```

输出说明

|        输出类型        | 说明                    |
| :--------------------: | ----------------------- |
| map[string]interface{} | 解析成功的数据用map存储 |

### Arg

```go
// 该接口可返回属性名为 “key” 的参数的属性值。
func Arg(key string) interface{} {}  
```

输入说明

| 输入参数 | 说明            |
| :------: | --------------- |
|   key    | 需要查询的key值 |

输出说明

|  输出类型   | 说明              |
| :---------: | ----------------- |
| interface{} | 根据key查询到的值 |

###  SuccessResult

```go
// 该接口可记录用户操作成功的信息，并将操作结果记录到链上。
func SuccessResult(msg string) {}  
```

输入说明

| 输入参数 | 说明               |
| :------: | ------------------ |
|   msg    | 成功操作的日志信息 |

### ErrorResult

```go
// 该接口可记录用户操作失败的信息，并将操作结果记录到链上。
func ErrorResult(msg string) {}
```

输入说明

输出说明

| 输入参数 | 说明               |
| :------: | ------------------ |
|   msg    | 失败操作的日志信息 |

### LogMessage

```go
// 该接口可记录事件日志。
func LogMessage(msg string) {}
```

输入说明

| 输入采纳数 | 说明               |
| :--------: | ------------------ |
|    msg     | 当前操作的日志信息 |

### GetCreatorOrgId

```go
// 获取合约创建者所属组织ID
func GetCreatorOrgId() string {}  
```

输出说明

| 输出类型 | 说明                 |
| :------: | -------------------- |
|  string  | 合约创建者所属组织ID |

### GetCreatorRole

```go
// 获取合约创建者角色
func GetCreatorRole() string {}  
```

输出说明

| 输出类型 | 说明           |
| :------: | -------------- |
|  string  | 合约创建者角色 |

### GetCreatorPk

```go
// 获取合约创建者公钥
func GetCreatorPk() string {} 
```

输出说明

| 输出类型 | 说明           |
| :------: | -------------- |
|  string  | 合约创建者公钥 |

### GetSenderOrgId

```go
// 获取交易发起者所属组织ID
func GetSenderOrgId() string {}  
```

输出说明

| 输出类型 | 说明                 |
| :------: | -------------------- |
|  string  | 交易发起者所属组织ID |

### GetSenderRole

```go
func GetSenderRole() string {} 
// 获取交易发起者角色
```

输出说明

| 输出类型 | 说明           |
| :------: | -------------- |
|  string  | 交易发起者角色 |

### GetSenderPk()

```go
func GetSenderPk() string {}  
// 获取交易发起者公钥
```

输出说明

| 输出类型 | 说明           |
| :------: | :------------- |
|  string  | 交易发起者公钥 |

### GetBlockHeight

```go
func GetBlockHeight() string {} 
// 获取当前区块高度
```

输出说明

| 输出类型 | 说明         |
| :------: | ------------ |
|  string  | 当前区块高度 |

### GetTxId

```go
func GetTxId() string {}
// 获取交易ID
```

输出说明

| 输出类型 | 说明   |
| :------: | ------ |
|  string  | 交易ID |

