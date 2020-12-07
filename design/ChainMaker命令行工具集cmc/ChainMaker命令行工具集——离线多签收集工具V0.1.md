# ChainMaker命令行工具集——离线多签收集工具V0.1

*2020.11.26 jasonruan*

## 0 前言

要实现多签收集，可选的方案有：

- 命令行`CLI`方式
- 离线网页方式
- 中心化服务方式
- 手机钱包方式
- 多签合约方式

本文采用命令行`CLI`方式，实现离线多签收集功能。

## 1 整体流程

- 生成待签名文件
- 线下提供给各组织
- 各组织使用有`Admin`权限私钥进行签名，生成已带签名串的`pb`二进制文件
- 线下从各组织收集已签名的`pb`文件，交由具有交易发送权限客户端
- 具有交易发送权限客户端收集所有已签名的`pb`文件，校验签名并检查待签名的内容是否一致，将小于半数的不一致的已签名文件进行剔除
- 构造交易并发送上链

## 2 多签类型

### 2.1 链配置更新

```protobuf
// config update type transaction payload
// TxType: UPDATE_CHAIN_CONFIG
message ConfigUpdatePayload {
    // endorsment signature with chain_id, redundant with TxHeader
    string chain_id = 1;
    // smart contract name
    string contract_name = 2;
    // update method
    string method = 3;
    // update parameters in k-v format
    repeated KeyValuePair parameters = 4;
    // config sequence, starts from 0 (genesis config)
    uint64 sequence = 5;
    // multi-sign, signature of [ConfigUpdatePayload] with endorsement = nil
    repeated EndorsementEntry endorsement = 6;
}
```

### 2.2 合约管理

```protobuf
// contract management type transaction payload
// TxType: CREATE_USER_CONTRACT & UPGRADE_USER_CONTRACT
message ContractMgmtPayload {
    // endorsment signature with chain_id, redundant with TxHeader
    string chain_id = 1;
    // smart contract name, set by contract creator, can have multiple versions
    ContractId contract_id = 2;
    // invoke method in bytes format
    string method = 3;
    // invoke parameters in bytes format
    repeated KeyValuePair parameters = 4; // 合约参数
    // 合约编译后的字节码
    bytes byte_code = 5;
    // payload signature, config_update|contract_mgmt type needed, multi-sign
    repeated EndorsementEntry endorsement = 6;
}
```

## 3 前置条件

### 3.1 链配置更新

- 获取当前链配置`sequence`

### 3.2 合约管理

- 将合约编译成字节码

## 4 实现说明

> 工具名称：cmc（ChainMaker CLI）

### 4.1 生成待签名文件

> 内容为`pb`二进制，提供命令将其转为`json`便于查看：
>
> ```bash
> ./cmc payload tojson [config|contract] --input collect.pb
> ```

#### 4.1.1 链配置

```bash
./cmc payload create config --chain-id chain1 --contract-name SYSTEM_CONTRACT_CHAIN_CONFIG --method CORE_UPDATE --kv-pair tx_scheduler_timeout:15;tx_scheduler_validate_timeout:20 --sequence 8 --output collect.pb
```

#### 4.1.2 合约管理

```bash
./cmc payload create contract --chain-id chain1 --contract-id counter001;1.0.0;WASMER_RUST --method init  --byte-code-path ./fact.wasm --output collect.pb
```

### 4.2 签名生成

> 说明：调用该命令，会在原`collect.pb`结构中添加一项`EndorsementEntry`，增加身份信息和签名
>
> ```protobuf
> message EndorsementEntry {
>     SerializedMember signer = 1;
>     bytes signature = 2;
> }
> ```

```bash
./cmc payload sign [config|contract] --input collect.pb --org-id wx-org1.chainmaker.org --admin-key-path ./admin1.sign.key --admin-crt-path ./admin1.sign.crt --output collect-signed.pb
```

### 4.3 签名合并

```bash
./cmc multisig payload merge [config|contract] --input collect-signed1.pb;collect-signed2.pb;collect-signed3.pb;collect-signed4.pb --output collect-signed-all.pb
```

### 4.4 交易发送

> 暂不用支持，待`golang SDK`实现，再进行实现

```bash
./cmc tx send --tx-type UPDATE_CHAIN_CONFIG  --org-id wx-org1.chainmaker.org --user-key-path ./client1.sign.key --user-crt-path ./client1.sign.crt --payload ./collect-signed-all.pb --node-addr https://127.0.0.1:12301
```

