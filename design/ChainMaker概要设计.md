# 1. 总述

## 1.1 ChainMaker概述

ChainMaker是将区块链深度模块化，抽象区块链整体执行流程，以支持广域场景的，用于生成区块链系统的标准、组件和工具集合。

本期概要设计着重于ChainMaker的模块化及各模块通用流程、接口的说明。

<u>从基础数据结构和接口设计上需考虑未来的规划，如：对于多链、跨链、多种共识算法、账本存储的可扩展性。具体模块内部设计优先针对本期实施范围细化。</u>

## 1.2 文档读者

ChainMaker的设计、研发、测试和系统运维人员。

## 1.3 术语解释



## 1.4 ChainMaker版本规划

- ChainMaker整体规划

|                   | **V1.0**（**2020.12**）            | **V2.0**                     | **V3.0**                 |
| ----------------- | ---------------------------------- | ---------------------------- | ------------------------ |
| 核心引擎          | 实现各子模块管理和调度             | 优化                         | 优化                     |
| 节点组网          | libp2p（4/7/10节点）               | 大范围组网优化               | 结合混合共识和多类型节点 |
| 身份权限管理      | 公私钥+白名单                      | 证书+细粒度权限（合约/操作） | DID                      |
| 共识算法          | BFT                                | hotstuff共识                 | 混合共识/竞争性共识/性能 |
| 智能合约          | WASM通用合约（go和java）           | WASM多语言                   | 其他合约引擎             |
| 交易调度          | 无冲突并行DAG                      | 有冲突并行DAG，合约重新执行  | 支持跨链的合约反向操作   |
| 交易接收和校验    | 队列持久化+防重                    | 双花验证，交易补偿           | 跨链交易验证             |
| 跨链              | --                                 | --                           | 中继方式的跨链           |
| 隐私保护          | --                                 | 聚合签名/零知识证明          | MPC                      |
| 账本存储          | levelDB/rocksDB                    | 定制合约账本管理/可分片/DAG  | 多存储适配               |
| 装配线            | --                                 | 定制版本配置自动生成         | 定制版本模块组装自动打包 |
| RPC接口、CLI及SDK | RPC服务端接口、CLI及SDK（go+java） |                              |                          |
| 密码算法          | go版本国密、ECDSA（确定曲线）      |                              |                          |
| 管理平台          | 基础配置，区块浏览，运行情况       | BaaS（节点管理、合约管理）   |                          |
| 监控&运维         | 资源和系统监控                     | 自动化发布，大屏监控         |                          |
| 测试工具          | 测试模式下mock模拟共识异常         | 自动化测试工具               |                          |

- V1.0版本两次迭代

|                   | 第一次迭代（2020.10）              | **V1.0**（**2020.12**）      |
| ----------------- | ---------------------------------- | ---------------------------- |
| 核心引擎          | 实现各子模块管理和调度             | 实现各子模块管理和调度       |
| 节点组网          | libp2p（4节点）  gRPC              | libp2p（4/7/10节点）         |
| 身份权限管理      | 公私钥+白名单                      | 公私钥+白名单                |
| 共识算法          | ***腾讯确定具体算法***             | BFT                          |
| 智能合约          | WASM通用合约（go和java）           | WASM通用合约（go和java）     |
| 交易调度          | 并行                               | 无冲突并行DAG                |
| 交易接收和校验    | 防重                               | 队列持久化+防重              |
| 跨链              | --                                 | --                           |
| 隐私保护          | --                                 | --                           |
| 账本存储          | levelDB                            | levelDB/rocksDB              |
| 装配线            | --                                 | --                           |
| RPC接口、CLI及SDK | RPC服务端接口、CLI及SDK（go+java） | --                           |
| 密码算法          | go版本国密、ECDSA（确定曲线）      |                              |
| 管理平台          |                                    | 基础配置，区块浏览，运行情况 |
| 监控&运维         |                                    | 资源和系统监控               |
| 测试工具          |                                    | 测试模式下mock模拟共识异常   |



# 2. 整体架构

## 2.1 区块产生流程

![ChainMaker区块产生流程图](./images/区块产生流程.png)

## 2.2 联盟链与公有链基本流程

![ChainMaker联盟链和公有链基本流程图](./images/联盟链和公有链基本流程.png)

## 2.3 模块架构

【腾讯】可先参照2.5小节流程设计

<img src="./images/system-module-new.png" alt="ChainMaker模块架构图" style="zoom:150%;" />



## 2.4 模块说明

- 核心引擎——核心引擎是链工厂的主程序，根据模块之间的依赖关系和配置参数调用其他模块， 完成区块链系统的整体功能；
- P2P——对节点p2p网络进行管理，实现节点发现，邻居管理、节点的状态管理、区块及交易消息同步和广播；
- 密码算法——密码学模块需实现加解密、哈希、认证与证明等基础功能接口，供其他模块使用；
- 身份权限管理——身份管理模块需实现权限配置与权限校验接口，对成员接入、智能合约调用等操作权限进行控制；
- 共识算法——共识算法模块需实现共识接口，输出在网络节点达成一致的区块数据；
- 智能合约——合约引擎模块需在资源受限的安全环境内模拟执行接口，根据给定的用户输入生成读写集合；
- 交易调度——交易调度模块需实现交易打包与排序接口，将输入的一批交易生成基于DAG的执行计划；
- 交易验证——对原始交易合法性的校验，如：交易签名合法性、防重、防双花等；
- TxPool交易池——缓存已验证的待出块合法交易，需支持批量操作，可按照预定规则批量持久化至磁盘；
- 账本存储——数据存储模块需实现最基本的CRUD接口，方便对数据库进行增删改查的操作，需支持多类型数据库的账本存储；
- 多语言SDK——符合系统RPC接入标准的SDK，方便应用系统接入，需支持多语言，如：java、go等。

## 2.5 整体流程

### 2.5.1 交易提交至交易池

![ChainMaker模块流程交易处理](./images/交易处理.png)

### 2.5.2 构建候选区块

![ChainMaker模块流程构建候选区块](./images/构建候选区块.png)

### 2.5.3 验证候选区块

![ChainMaker模块流程验证候选区块](./images/验证候选区块.png)

### 2.5.4 共识落块

![ChainMaker模块流程共识落块](./images/共识落块.png)

### 2.5.5 智能合约生命周期管理

![智能合约生命周期](./images/智能合约生命周期.png)



### 2.5.6 共识节点动态增删

【腾讯】

### 2.5.7 区块同步及验证

【腾讯，参考下图】

![同步模块](./images/同步模块.png)

# 3. 模块流程

## 3.1 核心引擎

### 3.1.1 模块流程

- 核心引擎初始化

![核心引擎初始化](./images/核心引擎初始化.png)

- 核心引擎交易处理

![核心引擎](./images/核心引擎.png)

### 3.1.2 模块接口

与网络和身份权限管理模块间采用接口调用或管道传递指针的方式

与其他模块采用管道或RPC+protobuf通信方式



## 3.2 P2P模块

模块功能：帮助上层管理复杂的p2p网路。

模块内部实现节点的状态管理、邻居管理、区块、交易消息同步和广播。对外提供的服务包括：区块、交易、共识消息的收发、节点增删的API接口。


```mermaid
 classDiagram
      P2P模块 .. 核心引擎 : 同步、广播、API管理
      共识模块 .. 核心引擎 : 共识消息
      class P2P模块{
          +PeerManager
          +SendMsg()
          +RecvMsg()
          +SendBlock()
          +RecvBlock()
          +SendTx()
          +RecvTx()
          +AddPeer()
          +RemovePeer()
      }
      class 核心引擎{
      }
      class 共识模块{
      }
```

采用libp2p作为连接库，使用KAD算法建立网络。

### 3.2.1 网络结构

![ChainMaker模块架构图](./images/网络结构.png)

### 3.2.2 节点类型

- 轻节点
  保存区块头信息以及自己相关的交易数据，通过全节点进行交易验证。轻节点参与交易和区块信息的全网广播。
- 全节点
  保存所有区块的数据，可以在本地直接验证交易数据的有效性。全节点参与交易和区块信息的全网广播。
- 种子节点
  新节点通过先连接到种子节点，然后发现其他节点。
- 共识节点
  共识节点必须是一个全节点，执行交易，生成DAG，打包成区块，并发送至其他共识节点进行共识。

### 3.2.3 节点状态

![ChainMaker模块架构图](./images/节点状态.png)

### 3.2.4 邻居发现过程

![ChainMaker模块架构图](./images/邻居发现过程.png)

## 3.3 密码算法模块

【腾讯，国密算法公钥及参数信息配置，与加密通信的结合】

> 该节以下为草稿，待讨论。

### 3.3.1 支持加密算法

> 一期优先来支持国密算法

- 对称密码
  - `SM4`
  - `AES`

- 非对称密码
  - `SM2`
  - `RSA`
  - `Secp256k1`
  - `ECDSA_P256`
  - `ECDSA_P384`
  - `ECDSA_P521`
  - `Ed25519`
- 哈希算法
  - `SM3`
  - `SHA256`
  - `Keccak256`

### 3.3.2 国密技术选型

| 可选方案                                                     | 特点                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [tjfoc](https://github.com/tjfoc)/[gmsm](https://github.com/tjfoc/gmsm) | 开源<br />纯Go语言实现<br />支持SM2/SM3/SM4                  |
| [guanzhi](https://github.com/guanzhi)/[GmSSL](https://github.com/guanzhi/GmSSL) | 开源<br />C实现<br />基于OpenSSL开发，保持了接口兼容，工具丰富<br />支持SM2/SM3/SM4/SM9/ZUC等国密算法 |
| [TencentSM](http://techmap.oa.com/project/9631)              | 腾讯自研，尚未开源                                           |

### 3.3.3 实现方案

```go
package crypto

// 秘钥类型
type KeyType int
const (
	// 对称秘钥
	AES KeyType = iota
	SM4

	// 非对称秘钥
	RSA
	Secp256k1
	SM2
	ECDSA_P256
	ECDSA_P384
	ECDSA_P521
	Ed25519
)

// === 秘钥接口 ===
type Key interface {
	// 获取秘钥字节数组
	Bytes() ([]byte, error)

	// 获取秘钥类型
	Type() KeyType
    
    // 获取编码后字符串格式秘钥
    String() (string, error)
}

// === 对称秘钥加解密接口 ===
type SymmetricKey interface {
	Key

	// 加密接口
	Encrypt(plain []byte) (cipher []byte, err error)

	// 解密接口
	Decrypt(cipher []byte) (plain []byte, err error)
}

// === 非对称秘钥签名+验签接口 ===
// 私钥签名接口
type PrivateKey interface {
	Key

	// 私钥签名
	Sign(data []byte) ([]byte, error)

	// 返回公钥
	PublicKey() PublicKey
}

// 公钥验签接口
type PublicKey interface {
	Key

	// 公钥验签
	Verify(data []byte, sig []byte) (bool, error)
}
```

### 3.3.4 提供接口

#### （1）非对称加密

```go
// 生成公私钥对
func GenerateKeyPair(keyType crypto.KeyType) (crypto.PrivateKey, error) {}
// func GenerateKeyPair(keyType crypto.KeyType) (pk string, sk string, err error) {}

// 签名
func Sign(keyType crypto.KeyType, sk string, data []byte) (sign string,  err error) {}

// 验签
func Verify(keyType crypto.KeyType, pk string, sign string, data []byte) (bool, error) {}
```

#### （2）对称加密

```go
// 创建对称秘钥对象
func GenerateSymKey(opt crypto.KeyType, key []byte) (crypto.SymmetricKey, error) {}

// 加密
func (symKey *SymKey) Encrypt(plain []byte) ([]byte, error) {}

// 解密
func (symKey *SymKey) Decrypt(crypted []byte) ([]byte, error) {}
```



## 3.4 身份权限管理

【腾讯，节点管理、证书配置、权限管理及其相应的数据模型的扩展】



## 3.5 共识算法

### 3.5.1 模块流程


![共识算法流程图](./images/consensus.png)

### 3.5.2 模块接口

模块内通用共识消息

```protobuf
enum ConsensusType {
  POW = 0;
  PBFT = 1;
  TENDERMINT = 2;
  HOTSTUFF = 3;
  RAFT = 4;
}

message ConsensusMsg {
  ConsensusType type = 1;
  string ChainId = 2;
  Signature Signature = 3;
  oneof msg {
    PoWMsg pow = 4;
    PBFTMsg pbft = 5;
    TendermintMsg tendermint = 6;
    HotstuffMsg hotstuff = 7;
    RAFTMsg raft = 8;
  }
}
```

各类共识算法需在此基础上扩展。

### 3.5.3 共识算法流程梳理

- BFT共识算法

【腾讯】




## 3.6 智能合约

### 3.6.1 模块说明

ChainMaker的智能合约虚拟机模块需要考虑以下问题：

- 隔离运行：每个虚拟机都运行在隔离的环境中，确保资源访问安全性，只能修改属于该合约自己的状态记录
- 合约终止：合约需要有执行终止条件，以限制对资源的消耗。终止条件可以是按照时间、指令数量、指令执行代价（类似ETH gas）等方式
- 智能合约开发环境：提供基于ChainMaker区块链的智能合约开发环境
- 轻量化实现：提供轻量级虚拟机，可以快速启动和快速运行，占用系统资源小
- 支持高级语言：支持Java、Go等高级语言的编写智能合约代码
- 测试和验证：提供测试合约代码，要求执行的结果是正确可验证的
- 低耦合设计：要求虚拟机可以在ChainMaker提供的数据接口上，就可以独立运行而不依赖其他的环境
- 跨合约调用：支持多层的跨合约相互调用
- 工具链和文档：提供各种工具、虚拟机设计实现文档、API文档，降低编写智能合约的入门难度
- 并行调用：ChainMaker将通过并行调用的方式，启动虚拟机

ChainMaker将能够支持多种形式的虚拟机，并且把虚拟机看作是一个黑匣子，为虚拟机提供统一的数据访问和密码算法访问接口。当一批交易被发送至虚拟机时，虚拟机将解析交易中的智能合约调用参数，并且在运行时，通过数据访问接口获取运行时必要的数据，最后执行生成交易的读写集、交易执行结果和交易执行的日志信息。

虚拟机本身应当是无状态的，不需要存储额外的数据。当交易批量地、持续地发送给虚拟机时，虚拟机需要并行启动多个实例来执行这些交易里对智能合约的调用。随后由ChainMaker来根据交易执行的读写集来分析和解决交易的冲突。

![智能合约](./images/outline.png)

### 3.6.2 接口设计

在ChainMaker的交易调度模块启动虚拟机时，会通过接口调用的方式调用，同时存储接口和密码算法接口将被**注入**到虚拟机中，供智能合约调用。

其中存储接口store_interface将能够对三类数据进行操作：

- ChainData：链数据库，主要包含历史区块、历史交易等数据
- StateDB：状态数据库，主要为智能合约的状态数据
- ReadWriteSet：读写集，智能合约类型的交易预执行过程中，虚拟机对ChainData和StateDB的模拟读写，将记录在ReadWriteSet。

#### 3.6.2.1 vm_interface接口

创建智能合约，该接口需要虚拟机实现，并由ChainMaker的交易调度模块来调用。

```
	import "chainmaker-go/pb"

type VM interface {

	//创建智能合约 ABI？
	// 入参
	// byteCode 字节码
	// input 构造函数入参
	// option 可选项
	// 返回
	// []*pb.TxRead 读集
	// []*pb.TxWrite 写集
	// []byte 结果
	// []byte 日志
	Create(byteCode []byte, input []byte, option map[string]string) ([]*pb.TxRead, []*pb.TxWrite, []byte, []byte)

	//执行智能合约
	// 入参
	// address 合约地址
	// input 函数入参
	// 返回
	// []*pb.TxRead 读集
	// []*pb.TxWrite 写集
	// []byte 结果
	// []byte 日志
	Call(address []byte, input []byte, ABI??) ([]*pb.TxRead, []*pb.TxWrite, []byte, []byte)
}

```


#### 3.6.2.2 store_interface接口

store_interface为虚拟机提供对ChainData、StateDB、ReadWriteSet的读写能力。（需要请百度的专家进行评估）

```
type ContractStore interface {
	//StateDB & ReadWriteSet
	//获取合约账户状态、Code
	ReadState(address []byte, key []byte) ([]byte, error)
	//写入合约账户状态
	WriteState(address []byte, key []byte, value []byte) error
	//删除合约账户状态
	DeleteState(address []byte, key []byte) error


	//ChainData
	//获取智能合约发布者
	GetPublisher(address []byte) []byte
	//获取智能合约的调用者
	GetCaller() []byte
	//获取当前区块高度
	GetCurrentBlockHeight() int64
	//获取当前区块链Hash
	GetCurrentBlockHash() []byte

	//Log
	WriteLog(address []byte, key []byte, value []byte) error
	
	//Result
	WriteResult(address []byte, key []byte, value []byte) error

}

```

### 3.6.3 字节码

- 编译，通过编译器，将高级语言java、go等编译为wasm字节码

- 存储，wasm字节码将以状态的形式存储在StateDB中，在创建智能合约的时候写入到StateDB
- 调用，在启动虚拟机执行时，字节码从StateDB中通过GetState获取到以后，以解释执行或者编译执行的方式运行。

### 3.6.4 合约终止

当智能合约被虚拟机中止执行时，应通过store_interface写入Result，表明终止的原因

## 3.7 交易调度

### 3.7.1 交易调度流程

![ChainMaker交易调度流程](./images/交易调度流程.png)

### 3.7.2 交易调度示意

- 构建读写集

|      | 读集       | 写集 | 结果 | 回执 |
| ---- | ---------- | ---- | ---- | ---- |
| Tx1  | Key1，Key2 | Key3 |      |      |
| Tx2  | Key1，Key2 |      |      |      |
| Tx3  | Key2       |      |      |      |
| Tx4  | Key3       |      |      |      |

- 构建DAG

根据读写集转换为下表，这个过程可以一边执行交易，一边生成。实际采用链表，每执行一笔交易，就可以分析读写冲突，并生成DAG里面的一个节点。

|      | Key1 | Key2 | Key3 |
| ---- | ---- | ---- | ---- |
| Tx1  | 读   | 读   | 写   |
| Tx2  | 读   | 读   |      |
| Tx3  |      | 读   |      |
| Tx4  |      |      | 读   |

- 交易调度示意图

![ChainMaker交易调度](./images/交易调度.png)



## 3.8 交易与区块校验

### 3.8.1 原始交易校验

![原始交易校验](./images/原始交易校验.png)

### 3.8.2 候选区块交易校验

![候选区块交易校验](./images/候选区块交易校验.png)

## 3.9 账本存储

### 3.9.1 模块功能说明

本期采用rocksDB或levelDB作为账本存储的数据库。

账本数据库存储结构详见第6章。

### 3.9.2 模块流程

![账本存储引擎](./images/DBEngine.png)

### 3.9.3 模块接口

面向核心引擎模块的接口

```go
type DbStore interface {
	DeleteBlock(blockHash types.Hash) error
	HasBlock(blockHash types.Hash) (bool, error)
	GetBlock(blockHash types.Hash) (*pb.Block, error)
	GetBlockAt(height int64) (*pb.Block, error)
	GetBlockSize(blockHash types.Hash) (int, error)
	PutBlock(block *pb.Block) error
	PutManyBlock(blocks []*pb.Block) error
	GetTx(transactionHash types.Hash) (*pb.Transaction, error)
	GetTxByTxid(txid []byte) (*pb.Transaction, error)
	GetTxs(TxKey []byte) ([]*pb.Transaction, error)
	QueryBlockHeader(blockid []byte) (*pb.Block, error)
	HasTx(txid []byte) (bool, error)
	UpdateLast(types.Hash) error
	GetLast() (types.Hash, error)
}

type Database interface {
	Open(path string, options map[string]interface{}) error
	Put(key []byte, value []byte) error
	Get(key []byte) ([]byte, error)
	Has(key []byte) (bool, error)
	Delete(key []byte) error
	Close()
	NewBatch() Batch
	NewIteratorWithRange(start []byte, limit []byte) Iterator
	NewIteratorWithPrefix(prefix []byte) Iterator
}

type Batch interface {
	ValueSize() int
	Write() error
	Reset()
	Put(key []byte, value []byte) error
	Delete(key []byte) error
	PutIfAbsent(key []byte, value []byte) error
	Exist(key []byte) bool
}

type Iterator interface {
	Key() []byte
	Value() []byte
	Next() bool
	Prev() bool
	Last() bool
	First() bool
	Error() error
	Release()
}
```

## 3.10 多语言SDK

【腾讯】



## 3.11 核心数据模型

【腾讯，补充身份权限管理、跨链等场景相关字段】

- block定义

block为区块总结构体，block结构按其三个子部分，即区块头、DAG读写集以及原始交易集，分别独立存储。

```go
type Block struct {
	Header  *Header        `protobuf:"bytes,2,opt,name=header,proto3" json:"header,omitempty"`
	Dag     *DAG           `protobuf:"bytes,3,opt,name=dag,proto3" json:"dag,omitempty"`
	Txs     []*Transaction `protobuf:"bytes,4,rep,name=txs,proto3" json:"txs,omitempty"`
}
```

- header定义

header即区块头，独立存储在KV库的不同Column Family里，CF命名可参考：ChainId（子链标识）+Header+扩展编号（000开始，未来分片扩展）。

key：BlockHeight+BlockHash。

value：按照下述结构图，其余字段，按照XX格式序列化后存储。

```go
type Header struct {
	ChainId           []byte      `protobuf:"bytes,1,opt,name=chain_id,json=chainId,proto3" json:"chain_id,omitempty"`                                  // 子链标识
	BlockHeight       int64       `protobuf:"varint,2,opt,name=block_height,json=blockHeight,proto3" json:"block_height,omitempty"`                     // 块高度
	PreBlockHash      []byte      `protobuf:"bytes,3,opt,name=pre_block_hash,json=preBlockHash,proto3" json:"pre_block_hash,omitempty"`                 // 前块哈希
	BlockHash         []byte      `protobuf:"bytes,4,opt,name=block_hash,json=blockHash,proto3" json:"block_hash,omitempty"`                            // 本块哈希（块标识）
	BlockVersion      []byte      `protobuf:"bytes,5,opt,name=block_version,json=blockVersion,proto3" json:"block_version,omitempty"`                   // 版本
	DagDigest         []byte      `protobuf:"bytes,6,opt,name=dag_digest,json=dagDigest,proto3" json:"dag_digest,omitempty"`                            // 保存DAG特征摘要
	StateRoot         []byte      `protobuf:"bytes,7,opt,name=state_root,json=stateRoot,proto3" json:"state_root,omitempty"`                            // 本块状态树根 非MPT
	MerkleRoot        []byte      `protobuf:"bytes,8,opt,name=merkle_root,json=merkleRoot,proto3" json:"merkle_root,omitempty"`                         // 本块merkle根
	BlockTimestamp    int64       `protobuf:"varint,9,opt,name=block_timestamp,json=blockTimestamp,proto3" json:"block_timestamp,omitempty"`            // 区块时间戳
	ProposerPublicKey []byte      `protobuf:"bytes,10,opt,name=proposer_public_key,json=proposerPublicKey,proto3" json:"proposer_public_key,omitempty"` // 提案节点标识（公钥）
	ConsensusArgs     []byte      `protobuf:"bytes,11,opt,name=consensus_args,json=consensusArgs,proto3" json:"consensus_args,omitempty"`               // 共识参数，此处存放影响块hash计算的信息
	AdditionalData    []byte      `protobuf:"bytes,12,opt,name=additional_data,json=additionalData,proto3" json:"additional_data,omitempty"`            // 扩展字段，此处存放不影响块hash计算的信息
	TxsCount          int64       `protobuf:"varint,13,opt,name=txs_count,json=txsCount,proto3" json:"txs_count,omitempty"`                             // 本块交易笔数，便于统计
	Signature         *Signature  `protobuf:"bytes,14,opt,name=signature,proto3" json:"signature,omitempty"`                                            // 提案者对本块签名
	QuorumCert        *QuorumCert `protobuf:"bytes,15,opt,name=quorum_cert,json=quorumCert,proto3" json:"quorum_cert,omitempty"`                        // 流水线BFT共识扩展，不参与区块哈希计算
}

type Signature struct {
	PublicKeys [][]byte `protobuf:"bytes,1,rep,name=public_keys,json=publicKeys,proto3" json:"public_keys,omitempty"`
    Signature  []byte   `protobuf:"bytes,2,opt,name=signature,proto3" json:"signature,omitempty"`
}
```


- 交易定义

交易独立存储在KV库的不同Column Family里，CF命名可参考：ChainId（子链标识）+Tx+扩展编号（000开始，未来分片扩展）。

key：TxId+BlockHeight+TxHash。

value：按照下述结构图，其余字段，按照XX格式序列化后存储。

```go
type Transaction struct {
	Metadata      *Transaction_MetaData `protobuf:"bytes,1,opt,name=metadata,proto3" json:"metadata,omitempty"`                                // 交易元数据
	Contracts     []*Contract           `protobuf:"bytes,2,rep,name=contracts,proto3" json:"contracts,omitempty"`                              // 合约调用
	Sender        []byte                `protobuf:"bytes,3,opt,name=sender_address,json=senderAddress,proto3" json:"sender_address,omitempty"` // 交易发送者
	Results       []*Transaction_Result `protobuf:"bytes,4,rep,name=results,proto3" json:"results,omitempty"`                                  // 合约执行返回
	TxHash        []byte                `protobuf:"bytes,5,opt,name=tx_hash,json=txHash,proto3" json:"tx_hash,omitempty"`                      // 交易哈希 contracts+sender_address
	Signature     *Signature            `protobuf:"bytes,6,opt,name=signature,proto3" json:"signature,omitempty"`                              // 交易签名 Contract+sender_address
	Payload       *Transaction_Payload  `protobuf:"bytes,7,opt,name=payload,proto3" json:"payload,omitempty"`                                  // 账户类数据
}

type Transaction_MetaData struct {
	ChainId     []byte `protobuf:"bytes,1,opt,name=chain_id,json=chainId,proto3" json:"chain_id,omitempty"`              // 子链标识
	Height      []byte `protobuf:"bytes,2,opt,name=height,proto3" json:"height,omitempty"`                               // 交易所属块高度
	TxId        []byte `protobuf:"bytes,3,opt,name=tx_id,json=txId,proto3" json:"tx_id,omitempty"`                       // 交易标识，便于外围应用系统检索本交易
	TxTimestamp int64  `protobuf:"varint,4,opt,name=tx_timestamp,json=txTimestamp,proto3" json:"tx_timestamp,omitempty"` // 交易时间戳
	Expiration  int64  `protobuf:"varint,5,opt,name=expiration,proto3" json:"expiration,omitempty"`                      // 交易有效期
}

type Contract struct {// TODO 合约标识、函数名、版本
	Type       Contract_ContractType `protobuf:"varint,1,opt,name=type,proto3,enum=pb.Contract_ContractType" json:"type,omitempty"`
	Parameters [][]byte              `protobuf:"bytes,2,rep,name=parameters,proto3" json:"parameters,omitempty"` // 合约参数
}

type Transaction_Result struct {
	Code               Transaction_Result_Code `protobuf:"varint,1,opt,name=code,proto3,enum=pb.Transaction_Result_Code" json:"code,omitempty"`
	Logs               [][]byte                `protobuf:"bytes,2,rep,name=logs,proto3" json:"logs,omitempty"`
	ReadWriteSetDigest []byte                  `protobuf:"bytes,3,opt,name=read_write_set_digest,json=readWriteSetDigest,proto3" json:"read_write_set_digest,omitempty"` // 读写集特征摘要
}

// UTXO交易相关
type Transaction_Payload struct {
	// Transaction input list
	TxInputs []*Transaction_TxInput `protobuf:"bytes,1,rep,name=tx_inputs,json=txInputs,proto3" json:"tx_inputs,omitempty"`
	// Transaction output list
	TxOutputs []*Transaction_TxOutput `protobuf:"bytes,2,rep,name=tx_outputs,json=txOutputs,proto3" json:"tx_outputs,omitempty"`
	// Mining rewards
	Coinbase []byte `protobuf:"bytes,3,opt,name=coinbase,proto3" json:"coinbase,omitempty"`
	// Random number used to avoid replay attacks
	Nonce int64 `protobuf:"varint,4,opt,name=nonce,proto3" json:"nonce,omitempty"`
	// 交易发起者, 可以是一个Address或者一个Account
	Initiator []byte `protobuf:"bytes,5,opt,name=initiator,proto3" json:"initiator,omitempty"`
	// 交易发起需要被收集签名的AddressURL集合信息，包括用于utxo转账和用于合约调用
	AuthRequire [][]byte `protobuf:"bytes,6,rep,name=auth_require,json=authRequire,proto3" json:"auth_require,omitempty"`
	// 交易发起者对交易元数据签名，签名的内容包括auth_require字段
	InitiatorSigns []*Signature `protobuf:"bytes,7,rep,name=initiator_signs,json=initiatorSigns,proto3" json:"initiator_signs,omitempty"`
	// 收集到的签名
	AuthRequireSigns []*Signature `protobuf:"bytes,8,rep,name=auth_require_signs,json=authRequireSigns,proto3" json:"auth_require_signs,omitempty"`
}

// UTXO交易的输入集
type Transaction_TxInput struct {
	// The transaction id referenced to
	RefTxid []byte `protobuf:"bytes,1,opt,name=ref_txid,json=refTxid,proto3" json:"ref_txid,omitempty"`
	// The output offset of the transaction referenced to
	RefOffset int32 `protobuf:"varint,2,opt,name=ref_offset,json=refOffset,proto3" json:"ref_offset,omitempty"`
	// The address of the launcher
	FromAddr []byte `protobuf:"bytes,3,opt,name=from_addr,json=fromAddr,proto3" json:"from_addr,omitempty"`
	// The amount of the transaction
	Amount int64 `protobuf:"varint,4,opt,name=amount,proto3" json:"amount,omitempty"`
	// Frozen height
	FrozenHeight int64 `protobuf:"varint,5,opt,name=frozen_height,json=frozenHeight,proto3" json:"frozen_height,omitempty"`
}

// UTXO交易的输出集
type Transaction_TxOutput struct {
	// The amount of the transaction
	Amount int64 `protobuf:"varint,1,opt,name=amount,proto3" json:"amount,omitempty"`
	// The address of the launcher
	ToAddr []byte `protobuf:"bytes,2,opt,name=to_addr,json=toAddr,proto3" json:"to_addr,omitempty"`
	// Fronzen height
	FrozenHeight int64 `protobuf:"varint,3,opt,name=frozen_height,json=frozenHeight,proto3" json:"frozen_height,omitempty"`
}
```

- DAG定义

DAG独立存储在KV库的不同Column Family里，CF命名可参考：ChainId（子链标识）+DAG+扩展编号（000开始，未来分片扩展）。

key：BlockHeight+BlockHash+DagDigest。

value：按照下述结构图，TxHashes和Vertexes，按照XX格式序列化后存储。

```go
type DAG struct {
	TxHashes [][]byte                `protobuf:"bytes,1,rep,name=tx_hashes,json=txHashes,proto3" json:"tx_hashes,omitempty"`
	Vertexes map[int32]*DAG_Neighbor `protobuf:"bytes,2,rep,name=vertexes,proto3" json:"vertexes,omitempty" protobuf_key:"varint,1,opt,name=key,proto3" protobuf_val:"bytes,2,opt,name=value,proto3"`
}

type DAG_Neighbor struct {
	Neighbors []int32 `protobuf:"varint,1,rep,packed,name=neighbors,proto3" json:"neighbors,omitempty"`
}
```

- 状态数据定义

待补充





# 4. 内部接口

【待双方下一阶段补充扩展】

## 4.1 协议说明

模块间交互使用protobuf进行消息序列化。

进程内模块使用管道交互，跨进程的模块基于RPC通信。

## 4.2 主要数据模型

```protobuf
syntax = "proto3";

package pb;
enum AccountType {
  Normal = 0;
}

message Contract {
  enum ContractType {
    CreateAccountContract = 0;
    TransferContract = 1;

    CreateSmartContract = 11;
    CallSmartContract = 12;
  }
  ContractType type = 1;
  bytes parameters = 2;// 合约参数
}

message TxRead {
  bytes key = 1;// 读集对应的key
  bytes ref_txid = 2;// 读集属于哪一个txid
  int32 ref_offset = 3;// 读集属于哪一个txid的哪一个offset
}

message TxWrite {
  bytes key = 1;// 写集对应的key
  bytes value = 2;// 写集对应的value
}

message Snapshot {
  bytes database_pointer = 1;
  repeated TxRead tx_reads = 3;
  repeated TxWrite tx_writes = 4;
}

message Transaction {
  message Result {
    enum Code {
      SUCCESS = 0;
      ILLEGAL_OPERATION = 1;
    }
    Code code = 1;
    repeated bytes logs = 2;
    bytes read_write_set_digest = 5;// 读写集特征摘要
  }

  message MetaData {
    bytes height = 1;// 交易所属块高度
    bytes tx_id = 2;// 交易标识，便于外围应用系统检索本交易
    int64 tx_timestamp = 3;// 交易时间戳
    int64 expiration = 4;// 交易有效期
    bytes ref_block = 5;// 本交易引用的块高度
  }

  MetaData metadata = 1;// 交易元数据
  Contract contract = 2;// 合约调用
  bytes sender_address = 3;// 交易发送者地址
  Result result = 4;// 返回
  bytes tx_hash = 5;// 交易哈希 Contract+sender_address
  Signature signature = 6;// 交易签名 Contract+sender_address
}

//使用邻接表存储DAG
//transaction_hashes里面存储了交易的拓扑排序
//vertexes按照拓扑排序后的顺序号表示交易
message DAG {
  message Neighbor {
    repeated int32 neighbors = 1;
  }
  repeated bytes tx_hashes = 1;
  map<int32, Neighbor> vertexes = 2;
}

message Signature {
  repeated bytes public_keys = 1; // TODO 算法类型
  bytes signature = 2;
}

message Block {
  Header header = 1;
  DAG dag = 2;
  repeated Transaction txs = 3;
}

message Header {
  int64 block_height = 1;// 块高度
  bytes pre_block_hash = 2;// 前块哈希
  bytes block_hash = 3;// 本块哈希（块标识）
  bytes block_version = 4;// 版本
  bytes dag_digest = 5;// 保存DAG特征摘要
  bytes state_root = 6;// 状态树根 非MPT
  int64 block_timestamp = 7;// 区块时间戳
  bytes proposer_public_key = 8;// 提案节点标识（公钥）
  bytes consensus_args = 9;// 共识参数，此处存放影响块hash计算的信息
  bytes additional_data = 10;// 扩展字段，此处存放不影响块hash计算的信息
  int64 txs_count = 11;// 本块交易笔数，便于统计
  Signature signature = 12;// 提案者对本块签名
}


```

### 4.1.1 节点广播



### 4.1.2 节点同步



## 4.2 消息类型

### 4.2.1 节点间心跳



### 4.2.2 节点数据同步

交易、区块

几种同步方式：Fast、Full

考虑拜占庭作恶

### 4.2.3 共识相关

区块（含DAG）、共识消息





# 5. 外部接口

【待双方下一阶段补充扩展】

## 5.1 协议说明



## 5.2 XXX接口



## 5.3 XXX接口



## 5.4 返回码



# 6. 账本KV存储结构

【腾讯】

## 6.1 区块存储结构

区块结构分为三部分，区块头、DAG、区块体（原始交易集），以KV的形式存储区块头：

key：BlockHeaderPrefix+ChainId+BlockNum.

value: {BlockHeader, DAG}，value包括区块头与DAG的序列化后的数据，以及交易的txid集合，并且DAG中已经包含区块txid的列表。

由于区块信息散步在多个kv项中，查询一个完整的区块需要查询多个kv数据再合并成一个完整的区块，先查询区块链头，恢复区块头与DAG，再根据DAG中的txid列表，依次查询交易数据恢复交易集列表，组成区块体

## 6.2 交易存储结构

Key：TransactionPrefix+ChainId+TxID

Value: Transaction序列化后的数据

## 6.3 读写集存储结构

读写集与交易关联，存储txid与读写集的kv集

Key: RWSetPrefix+ChainId+TxID

Value: [{ContractId, TxRead, TxWrite}, …]

## 6.4 状态数据存储结构
状态数据按合约的ContractId进行分表。

Key: StateWorldPrefix+ChainId+ContractId+stateKey

Value: {stateValue, version}， version由blockNum+txNum

## 6.5 索引存储结构
1. blockHash -> blockNum
2. blockNum+txNum -> txid

# 7. 其他模块

## 7.1 管理平台

主要功能

1. 区块链系统Dashboard：系统节点数、全网节点状态、区块总高度、总交易数、近10个区块信息（动态更新）
2. 节点管理，节点所属机构信息、节点部署信息、节点公钥地址、节点服务的自动化部署
3. 智能合约管理，智能合约版本管理，智能合约发布管理
4. 区块浏览器，区块查询（按块哈希、块高度）、交易查询（按TxID、交易哈希）

## 7.2 监控&运维

可采用Grafana+prometheus或Zabbix

系统级监控：监控服务器资源，包括：CPU、内存、磁盘、网络IO等。

应用级监控：监控区块链系统的关键指标，如：进程的内存使用、进程CPU使用、进程文件句柄数情况、Txpool大小、出块时间、共识状态、智能合约引擎执行情况、交易接收和处理吞吐量等。

## 7.3 测试工具

ChainMaker借鉴测试驱动开发的思想，在研发阶段注重单元测试

本系统具有完整且配套的测试用例。

对于拜占庭错误的复杂异常场景，ChainMaker的核心模块应具有mock能力，在测试模式下，可以按照预置的规则模拟异常情况（如篡改消息、超时响应、重放通信报文等），方便测试。

# 8. 日志说明

## 8.1 日志文件规划

区块链系统日志文件分为系统日志、简要日志、事件日志、异常交易处理日志四类。不要向进程标准输出文件中写入日志的数据。系统日志、简要日志、事件日志，建议按小时切分；异常交易处理日志，建议按日切分。

四类日志文件：

- 系统日志：可理解为详细日志，包含系统关键处理节点的日志信息，如：交易接收、交易验证、候选区块产生、智能合约调用、合约调用结果、共识各投票阶段、账本数据库操作等。为了日志检索方便，每行日志中应包含具有唯一性的标识，如：TxID、TxHash、BlockHeight、BlockHash等。每行日志的时间戳精确到毫秒。
- 简要日志：标识没比Tx或Block的处理结果，每个Tx或Block在最终处理结束（落块）后记录，只记录一行。便于快速分析系统运行情况，如：系统是否存活、系统吞吐量、各类Tx的处理汇总统计等。
- 事件日志：区块链系统事件处理的一种方式，在交易落块后，如果需要对该交易做后续处理，需在事件日志中记录，一笔交易一行。
- 异常交易处理日志：某些可以预见且可以补救的处理异常，将Tx相关数据记录在此日志中，可用于系统恢复后的交易补偿处理。

## 8.2 日志级别规划

ChainMaker使用四种日志级别：

Debug：调试使用，在正式环境运行时会屏蔽掉。

Info：正式环境设置的日志级别，仅在系统处理的关键环节输出日志。

Warn：可以预判到的异常情况，且有异常处理机制，不会导致数据不一致、系统被挂起等严重问题，打印此日志进行告警。

Error：未预判的异常情况，异常处理机制不完备，可能对系统可用性、安全性、一致性带来影响。

日志模块可使用zap。









