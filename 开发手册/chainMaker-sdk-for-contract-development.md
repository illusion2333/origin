# 智能合约开发 SDK

ChainMaker 的智能合约支持多语言开发，目前已支持 C、Go 和 Rust三种语言。上述三种语言都使用 WASM 规范。Rust 合约的性能较好，但是编写难度较大；C 合约的性能和编写难度比较适中；Go 合约使用起来更方便。ChainMaker分别为三种语言开发提供了SDK，方便开发者使用不同的语言进行合约开发。

## SDK接口说明

### 接口分类

- 数据存取相关接口
- 参数获取相关接口
- 事件日志相关接口

### Go sdk API

#### GetState

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

#### GetStateFromKey

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

#### PutState

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

#### PutStateFromKey

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

#### DeleteState

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

#### getArgsMap()

```go
// 该接口可把 json 格式的数据反序列化，并将解析出的数据存于一个 map[string]interface{} 类型的全局变量。
func getArgsMap() error {} 
```

输出说明

| 输入参数 | 说明         |
| :------: | ------------ |
|   nil    | 数据解析失败 |

#### Args

```go
// 该接口调用 getArgsMap() 接口，把 json 格式的数据反序列化，并将解析出的数据返还给用户。
func Args() map[string]interface{} {}  
```

输出说明

|        输出类型        | 说明                    |
| :--------------------: | ----------------------- |
| map[string]interface{} | 解析成功的数据用map存储 |

#### Arg

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

####  SuccessResult

```go
// 该接口可记录用户操作成功的信息，并将操作结果记录到链上。
func SuccessResult(msg string) {}  
```

输入说明

| 输入参数 | 说明               |
| :------: | ------------------ |
|   msg    | 成功操作的日志信息 |

#### ErrorResult

```go
// 该接口可记录用户操作失败的信息，并将操作结果记录到链上。
func ErrorResult(msg string) {}
```

输入说明

输出说明

| 输入参数 | 说明               |
| :------: | ------------------ |
|   msg    | 失败操作的日志信息 |

##### LogMessage

```go
// 该接口可记录事件日志。
func LogMessage(msg string) {}
```

输入说明

| 输入采纳数 | 说明               |
| :--------: | ------------------ |
|    msg     | 当前操作的日志信息 |

#### GetCreatorOrgId

```go
// 获取合约创建者所属组织ID
func GetCreatorOrgId() string {}  
```

输出说明

| 输出类型 | 说明                 |
| :------: | -------------------- |
|  string  | 合约创建者所属组织ID |

#### GetCreatorRole

```go
// 获取合约创建者角色
func GetCreatorRole() string {}  
```

输出说明

| 输出类型 | 说明           |
| :------: | -------------- |
|  string  | 合约创建者角色 |

#### GetCreatorPk

```go
// 获取合约创建者公钥
func GetCreatorPk() string {} 
```

输出说明

| 输出类型 | 说明           |
| :------: | -------------- |
|  string  | 合约创建者公钥 |

#### GetSenderOrgId

```go
// 获取交易发起者所属组织ID
func GetSenderOrgId() string {}  
```

输出说明

| 输出类型 | 说明                 |
| :------: | -------------------- |
|  string  | 交易发起者所属组织ID |

#### GetSenderRole

```go
func GetSenderRole() string {} 
// 获取交易发起者角色
```

输出说明

| 输出类型 | 说明           |
| :------: | -------------- |
|  string  | 交易发起者角色 |

#### GetSenderPk()

```go
func GetSenderPk() string {}  
// 获取交易发起者公钥
```

输出说明

| 输出类型 | 说明           |
| :------: | :------------- |
|  string  | 交易发起者公钥 |

#### GetBlockHeight

```go
func GetBlockHeight() string {} 
// 获取当前区块高度
```

输出说明

| 输出类型 | 说明         |
| :------: | ------------ |
|  string  | 当前区块高度 |

#### GetTxId

```go
func GetTxId() string {}
// 获取交易ID
```

输出说明

| 输出类型 | 说明   |
| :------: | ------ |
|  string  | 交易ID |



### C++ SDK API

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

## 编译方法

### go合约编译方法

Go 版本的智能合约开发 SDK 使用 TinyGo 编译器，将用 Go 语言编写的智能合约编译为 WASM 字节码。在编译的时候，需要将 SDK 和用户编写的智能合约放入同一个文件夹，并在此文件夹的当前路径执行如下编译命令：

```shell
tinygo build -no-debug -opt=s -o name.wasm -target wasm
```

命令中 “name.wasm” 为生成的WASM 字节码的文件名，由用户自行指定。

### c++合约编译方法

C++ 版本的智能合约使用emcc 1.38.48版本编译器进行编译，并需要使用emcc 编译 protobuf ，protobuf 使用3.7.1版本，编译之后执行emmake make。

### 使用 docker 编译得到 WASM 文件

在使用我们所提供的SDK编写好智能合约后，我们提供了一个docker环境，用户只需把其编写的智能合约作为输入，即可得到相应的WASM文件。用户可以自己搭建本地的 docker 环境；也可使用我们提供的 http 接口，通过服务器来编译得到 WASM 文件。

#### 本地配置 WASM 的 docker 编译环境 

##### 导入 docker 镜像

```bash
docker import ubuntu.tar wasm/ubuntu:v1.0
```

“ubuntu.tar” ：我们所提供的 docker；

“wasm/ubuntu:v1.0”：“wasm/ubuntu”为导入后的镜像名，可自定义，“v1.0”为镜像的说明信息，也可自定义。

##### 运行 docker

```bash
docker run -p 8000:8080 -it wasm/ubuntu:v1.0 /bin/bash
```

在运行 docker 的时候，必须暴露出一个端口，“8000:8080”表示把docker的 8080 端口映射到宿主机的 8000 端口，我们通过访问宿主机的 8000 端口即可使用 docker 所提供的服务。

也可使用以下命令运行 docker，和刚才的命令相比，此句命令把宿主机的“文件夹”映射到了docker下的“docker_for_wasm”文件夹，这样可以方便的在 docker 中使用宿主机的文件。

```shell
docker run -p 8000:8080 -itv /home/cm/chainmaker/docker_for_wasm:/usr/local/get-wasm/file  wasm/ubuntu:v1.0 /bin/bash
```

##### 后台运行 server.go

```shell
nohup  go run server.go &
```

nohup 可以使得程序挂载在后台。

###### 上传智能合约源文件并返回 WASM 文件

```shell
curl -F "file=@main.c" -X POST 0.0.0.0:8000 --output main.wasm
```

当运行 docker 后，在宿主机的终端中执行此条命令行语句，即可把自己的合约上传并返回一个 WASM 文件。“main.c”需要修改为用户自己的智能合约，“main.wasm”为接收到的 WASM 文件的名字，可由用户自己定义。“0.0.0.0”表示当前设备的 IP，即docker宿主机的 IP

##### docker http接口

我们已经在服务器配置了支持 C、Go 和 Rust 语言智能合约翻译为 WASM 字节码的编译环境。使用我们所提供的接口，即可获取智能合约的 SDK，并且可以把编写好的智能合约编译成为 WASM 字节码。

POST 请求：http://192.168.1.120:8000/getsdk

参数：

| 属性 |  类型  |                  描述                  |
| :--: | :----: | :------------------------------------: |
| type | string | 指定要获取的 SDK 版本（C、Go 或 Rust） |

使用该接口后会返回相应版本的智能合约开发 SDK，例如：

```shell
curl -d "type=go" -X POST http://192.168.1.120:8000/getsdk --output go.sdk
```

上述命令指定获取 Go 版本的 SDK，得到的是 .tar.gz 类型的压缩文件，“go.sdk”是压缩文件的名称，可由用户自己指定。

POST 请求：http://192.168.1.120:8000/towasm

参数：

| 参数 | 类型   | 说明                                                         |
| ---- | ------ | :----------------------------------------------------------- |
| type | string | 指定需要编译的智能合类型                                     |
| file | file   | 智能合约源文件（需把包含 SDK 在内的所有文件打包成一个文件上传，打包类型需为.tar.gz） |

示例：

```shell
curl -F "type=go" -F "file=@go.tar.gz"  -X POST http://192.168.1.120:8000/towasm --output go.wasm
```

该命令把智能合约的源文件（已提前打包）上传至服务器，返回得到对于的 WASM 文件，"type=go"表示需要编译的 Go 类型的文件，"file=@go.tar.gz"表示把智能合约源文件的压缩包 go.tar.gz 上传至服务器，“go.wasm” 表示服务器返回的 WASM 文件，名字可由用户自己指定。

## 智能合约编写示例

下面的代码示例说明了关键接口的使用方法。

```go
func init() {     
    // 使用 PutState() 接口，可以写入合约信息并传到链上。
	  PutState("fruit", "apple", "1") 	
    PutState("fruit", "banana", "1")
    PutState("meat", "pork", "1") 	
    PutState("meat", "chicken", "1") 
}  

func query() {     
   // 使用 Args() 接口可以解析用户的输入参数，并存于一个 map 变量   	
   // 此处 m 为 map[string]interface{} 类型的变量，开发者可通过其去获取想要的数据 	
    m := Args() 	
    category := m["category"].(string)     
    // 等价于 name := m["name"].(string)，Args() 与 Arg() 的作用几乎一致，不过当需要获取多个参数时，使用 Args() 程序效率更高，只需要解析一次用户输入，就可多次获取数据。而 Arg() 获取一次参数就需要解析一次。
    name := Arg("name").(string)     
    // 使用 GetState() 接口，可以从链上读取合约信息。
	  stateOld := GetState(category, name)     
    // 把用户操作写入日志 	
    SuccessResult(name + " query state: " + stateOld) 	         
    LogMessage("ok: " + name + " query state: " + stateOld) 
}
```

#### 