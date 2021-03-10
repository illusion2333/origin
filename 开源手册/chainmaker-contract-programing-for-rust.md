# ChainMaker Contract Programing for rust

[TOC]

## 1 合约编写流程


## 1.1 使用Docker镜像进行合约开发

ChainMaker官方已经将容器发布至GitHub

拉取镜像
```
docker pull huzhenyuan/chainmaker-rust-contract:1.0.0
```

请指定你本机的工作目录$WORK_DIR，例如C:\tmp，挂载到docker容器中以方便后续进行必要的一些文件拷贝

```
docker run -it --name chainmaker-rust-contract -v $WORK_DIR:/home huzhenyuan/chainmaker-rust-contract:1.0.0 bash
```

编译合约

```
# cd /home/
# tar xvf /data/contract_rust_template.tar.gz
# cd contract_rust
# wasm-pack build
```

生成合约的字节码文件在

```
/home/contract_rust/main.wasm
```

### 1.2 框架描述

解压缩contract_rust_template.tar.gz后，文件描述如下：

- Cargo.toml： 工程配置，参考：https://rustwasm.github.io/wasm-pack/book/cargo-toml-configuration.html
- src： 源码目录
  - lib.rs： SDK入口
  - sim_context.rs：主要SDK工具类，详情接口见下方[Rust SDK API](#api)描述
  - vec_box.rs： 内存管理工具类
  - [fact.rs](#fact)：存证示例代码
  - counter.rs： 计数器示例代码

### 1.3 示例代码说明

**存证合约示例：fact.rs <span id="fact"></span>** 实现功能

1、存储文件哈希和文件名称和该交易的ID。

2、通过文件哈希查询该条记录

```rust
use crate::sim_context;
// 安装合约时会执行此方法，必须
#[no_mangle]
pub extern "C" fn init_contract() {
    sim_context::log("init_contract");
    // 安装时的业务逻辑，可为空
}

// 升级合约时会执行此方法，必须
#[no_mangle]
pub extern "C" fn upgrade() {
    sim_context::log("upgrade");
    // 升级时的业务逻辑，可为空
}

// 保存
#[no_mangle]
pub extern "C" fn save() {
    // 获取上下文
    let ctx = &mut sim_context::get_sim_context();

    // 获取传入参数
    let file_hash = ctx.arg_default_blank("file_hash");
    let file_name = ctx.arg_default_blank("file_name");

    // 构建json object
    let object = object! {
        file_hash: file_hash.clone(),
        file_name: file_name,
    };
    // 存储
    ctx.put_state("fact", &file_hash, object.to_string().as_bytes());
}

// 查询
#[no_mangle]
pub extern "C" fn find_by_file_hash() {
    // 获取上下文
    let ctx = &mut sim_context::get_sim_context();
    
    // 获取传入参数
    let file_hash = ctx.arg_default_blank("file_hash");
    
    // 校验参数
    if file_hash.len() == 0 {
        ctx.log("file_hash is null");
        ctx.ok("");
        return;
    }

    // 查询
    let (fact_vec, code) = ctx.get_state("fact", &file_hash);
    
    // 校验返回结果
    if code != SUCCESS_CODE {
        ctx.log("get_state fail");
        ctx.error("get_state fail");
        return;
    }
    if fact_vec.len() == 0 {
        ctx.log("None");
        ctx.ok("");
        return;
    }
    
    // 序列化
    let fact_str = std::str::from_utf8(&fact_vec).unwrap();

    // 打印日志
    ctx.log(fact_str);
    // 返回查询结果
    ctx.ok(fact_str);
}

```



### 1.4 代码编写规则

**对链暴露方法写法为：**

- #[no_mangle]
- pub extern "C"
- method_name(): 不可带参数，无返回值

```rust
#[no_mangle]// no_mangle注解，表明对外暴露方法名称不可变
pub extern "C" fn init_contract() { // pub extern "C" 集成C
    // todo 
}
```

**其中init_contract、upgrade方法必须有且对外暴露**

- init_contract：创建合约会执行该方法
- upgrade： 升级合约会执行该方法

```rust
// 安装合约时会执行此方法。ChainMaker不允许用户直接调用该方法。
#[no_mangle]
pub extern "C" fn init_contract() {
    // dosome thing
}

// 升级合约时会执行此方法。ChainMaker不允许用户直接调用该方法。
#[no_mangle]
pub extern "C" fn upgrade() {
    
}
```

**获取与链交互的上下文sim_context**

1、在`lib.rs`中引入sim_context

```rust
pub mod sim_context;
```

2、在使用时引入sim_context

```rust
use crate::sim_context;

fn method_name() {
    // 获取上下文
    let ctx = &mut sim_context::get_sim_context();
    
}

```





### 1.5 编译说明

在ChainMaker IDE中集成了编译器，可以对合约进行编译，集成的rust编译器是`rustc: 1.48.0`，wasm编译器是`wasm-pack: 0.9.1`， 采用默认cargo管理包，版本为`cargo: 1.49.0`， 默认提供[json: 0.12.4](https://crates.io/crates/json)库，合约支持在线其他轻量级序列化方式。用户如果手工编译需在项目根目录执行命令： `wasm-pack build --release`，会在target中生成wasm文件。

## 2 合约发布过程

请参考：[《chainmaker-go-sdk.md》](./chainmaker-go-sdk.md)4.1.5 发送创建合约请求，或者[《chainmaker-java-sdk.md》](./chainmaker-java-sdk.md)2.1.4 创建合约。

## 3 合约调用过程

请参考：[《chainmaker-go-sdk.md》](./chainmaker-go-sdk.md)4.1.7 合约调用，或者[《chainmaker-java-sdk.md》](./chainmaker-java-sdk.md)2.1.7 执行合约。


## 4 Rust SDK API描述 <span id="api"></span>

### 内置链交互接口

用于链与SDK数据交互，用户无需关心。

```rust
// 申请size大小内存，返回该内存的首地址
pub extern "C" fn allocate(size: usize) -> i32 {}
// 释放某地址
pub extern "C" fn deallocate(pointer: *mut c_void) {}
// 获取SDK运行时环境
pub extern "C" fn runtime_type() -> i32 {}
```



### 用户与链交互接口

#### get_state

```rust
// 获取合约账户信息。该接口可从链上获取类别 “key” 下属性名为 “field” 的状态信息。
// @param key: 需要查询的key值
// @param field: 需要查询的key值下属性名为field
// @return1: 查询到的value值
// @return2: 0: success, 1: failed
pub fn get_state(&mut self, key: &str, field: &str) -> (Vec<u8>, result_code) {}
```

#### get_state_from_key

```rust
// 获取合约账户信息。该接口可以从链上获取类别为key的状态信息
// @param key: 需要查询的key值
// @return1: 查询到的值
// @return2:  0: success, 1: failed
pub fn get_state_from_key(&mut self, key: &str) -> (Vec<u8>, result_code) {}
```

#### put_state

```rust
// 写入合约账户信息。该接口可把类别 “key” 下属性名为 “filed” 的状态更新到链上。更新成功返回0，失败则返回1。
// @param key: 需要存储的key值
// @param field: 需要存储的key值下属性名为field
// @param value: 需要存储的value值
// @return: 0: success, 1: failed
pub fn put_state(&mut self, key: &str, field: &str, value: &[u8]) -> result_code {}
```

#### put_state_from_key

```rust
// 写入合约账户信息。
// @param key: 需要存储的key值
// @param value: 需要存储的value值
// @return: 0: success, 1: failed
pub fn put_state_from_key(&mut self, key: &str, value: &[u8]) -> result_code {}
```

#### delete_state

```rust
// 删除合约账户信息。该接口可把类别 “key” 下属性名为 “name” 的状态从链上删除。
// @param key: 需要删除的key值
// @param field: 需要删除的key值下属性名为field
// @return: 0: success, 1: failed
pub fn delete_state(&mut self, key: &str, field: &str) -> result_code {}
```

#### delete_state_from_key

```rust
// 删除合约账户信息。该接口可把类别 “key” 下属性名为 “name” 的状态从链上删除。
// @param key: 需要删除的key值
// @return: 0: success, 1: failed
pub fn delete_state_from_key(&mut self, key: &str) -> result_code {}
```

#### args

```rust
// 该接口将用户传入的参数以json对象的形式全量返回
// @return: JsonValue，json用法参考：https://docs.rs/json/0.12.4/json/
pub fn args(&self) -> &JsonValue {}
```

#### arg

```rust
// 该接口可返回属性名为 “key” 的参数的属性值。
// @param key: 获取的参数名
// @return: 获取的参数值 或 错误信息。当未传该key的值时，报错param not found
pub fn arg(&self, key: &str) -> Result<String, String> {}
```

#### arg_default_blank

```rust
// 该接口可返回属性名为 “key” 的参数的属性值。
// @param key: 获取的参数名
// @return: 获取的参数值。当未传该key的值时，返回空字符串
pub fn arg_default_blank(&self, key: &str) -> String {}
```

####  ok

```rust
// 该接口可记录用户操作成功的信息，并将操作结果记录到链上。
// @param body: 成功返回的信息
pub fn ok(&mut self, body: &str) -> result_code {}
```

#### error

```rust
// 该接口可记录用户操作失败的信息，并将操作结果记录到链上。
// @param body: 失败信息
pub fn error(&self, body: &str) -> result_code {
```

#### log

```rust
// 该接口可记录事件日志。
// @param msg: 事件信息
pub fn log(msg: &str) {
```

#### get_creator_org_id

```rust
// 获取合约创建者所属组织ID
// @return: 合约创建者的组织ID
pub fn get_creator_org_id(&self) -> String {}
```

#### get_creator_role

```rust
// 获取合约创建者角色
// @return: 合约创建者的角色
pub fn get_creator_role(&self) -> String {}  
```

#### get_creator_pub_key

```rust
// 获取合约创建者公钥
// @return: 合约创建者的公钥的SKI
pub fn get_creator_pub_key(&self) -> String {} 
```

#### get_sender_org_id

```rust
// 获取交易发起者所属组织ID
// @return: 交易发起者的组织ID
pub fn get_sender_org_id(&self) -> String {}
```

#### get_sender_role

```rust
// 获取交易发起者角色
// @return: 交易发起者角色
pub fn get_sender_role(&self) -> String {}  
```

#### get_sender_pub_key()

```rust
// 获取交易发起者公钥
// @return 交易发起者的公钥的SKI
pub fn get_sender_pub_key(&self) -> String {} 
```

#### get_block_height

```rust
// 获取当前区块高度
// @return: 当前块高度
pub fn get_block_height(&self) -> i32 {}
```

#### get_tx_id

```rust
// 获取交易ID
// @return 交易ID
pub fn get_tx_id(&self) -> String {}
```