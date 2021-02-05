# 长安链 · ChainMaker Operation Manual

## 1 脚本说明

脚本文件主要放在源码的tools和script路径下，分别说明如下：

cmc：ChainMaker命令行工具，一方面cmc可以通过rpc和节点进行通信，完成包括证书管理、合约管理、链配置管理、链信息查询等多项功能。另一方面也有本地的ca证书生成，用户和节点证书的签发等多项功能。

prepare.sh: 用来批量生成证书和节点配置。可以使用参数来指定节点数量和链数量

build_release.sh: 用来编译并打包完整的启动包，启动包内部包含了prepare生成的证书和配置文件，打包之后的文件放置在build/release下面，直接拷贝到需要部署的机器上解压修改监听地址后即可运行。也可以直接在本地调用cluster_quick_start.sh来快速启动多个节点。

cluster_quick_start.sh: 快速启动脚本，可以同时在本地启动多节点，程序路径在build/release下面。

cluster_quick_stop.sh: 快速停止脚本，可以同时把本地启动的多节点停止。

## 2 部署路径说明

启动包解压后主要包含的目录说明如下：

|-- bin                          # 可执行程序路径
|   |-- chainmaker     # chainmaker主程序
|   |-- restart.sh         # 重启脚本
|   |-- start.sh             # 启动脚本
|   -- stop.sh               #停止脚本
|-- config                    # 配置文件路径
|   -- wx-org1.chainmaker.org     # 组织1配置文件路径
|       |-- certs                                  # 证书路径
|       |   |-- ca                                  # 存放所有组织根证书的目录，每个组织有一个以组织名称命名的文件夹
|       |   |-- node                             # 节点证书路径
|       |   |   |-- common1               # 普通证书路径，证书可用于RPC和网络
|       |   |   -- consensus1              # 共识证书路径，证书可用于RPC和网络
|       |   -- user                                # 用户证书路径
|       |       |-- admin1                    # 管理员用户证书路径
|       |       -- client1                        # 普通用户证书路径
|       |-- chainconfig                      # 链配置路径
|       |   -- bc1.yml                          # 链配置文件路径
|       |-- chainmaker.yml              #主配置文件路径
|       -- log.yml                                # 日志配置文件路径
|-- data                                            # 数据库存储路径
|-- lib                                               # 依赖动态库路径
|   -- libwasmer_runtime_c_api.so
-- log                                                # 日志文件路径

## 3 配置说明

#### 3.1 节点配置chainmaker.yml

```yaml
# 配置链Id和对应的链配置文件
blockchain:
  - chainId: chain1
    genesis: chainconfig/bc1.yml

# 配置节点信息
node:
# 节点类型：full、spv
  type:              full
# 组织Id
  org_id:            wx-org1.chainmaker.org
  priv_key_file:     ./certs/node/consensus1/consensus1.sign.key
  cert_file:         ./certs/node/consensus1/consensus1.sign.crt
  signer_cache_size: 1000
  cert_cache_size:   1000

# 配置节点网络信息
net:
  provider: LibP2P
  listen_addr: /ip4/0.0.0.0/tcp/11301
  tls:
    enabled: true
    priv_key_file: ./certs/node/consensus1/consensus1.tls.key
    cert_file:     ./certs/node/consensus1/consensus1.tls.crt

txpool:
  max_txpool_size: 5120 # 普通交易池上限
  max_config_txpool_size: 10 # config交易池的上限
  full_notify_again_time: 30 # 交易池溢出后，再次通知的时间间隔(秒)

rpc:
  provider: grpc
  port: 12301
  tls:
    # TLS模式:
    #   disable - 不启用TLS
    #   oneway  - 单向认证
    #   twoway  - 双向认证
    #mode: disable
    #mode: oneway
    mode:           twoway
    priv_key_file:  ./certs/node/consensus1/consensus1.tls.key
    cert_file:      ./certs/node/consensus1/consensus1.tls.crt

monitor:
  enabled: false
  port: 14321

pprof:
  enabled: false
  port: 24321

storage:
  provider: LevelDB
  store_path: ../data/ledgerData
```

#### 3.2 链配置bc*.yml

```yaml
chain_id: chain1        # 链标识
version: v1.0.0         # 链版本
sequence: 1             # 配置版本
auth_type: "identity"   # 认证类型

crypto:
  hash: SHA256

# 交易、区块相关配置
block:
  tx_timestamp_verify: true # 是否需要开启交易时间戳校验
  tx_timeout: 600           # 交易时间戳的过期时间(秒)
  block_tx_capacity: 100    # 区块中最大交易数
  block_size: 10            # 区块最大限制，单位MB
  block_interval: 2000      # 出块间隔，单位:ms

# core模块
core:
  tx_scheduler_timeout: 10          #  [0, 60] 交易调度器从交易池拿到交易后, 进行调度的时间
  tx_scheduler_validate_timeout: 10 # [0, 60] 交易调度器从区块中拿到交易后, 进行验证的超时时间

#共识配置
consensus:
  # 共识类型(3-TBFT,6-SOLO)
  type: 3
  # 共识节点列表，组织必须出现在trust_roots的org_id中，每个组织可配置多个共识节点，节点地址采用libp2p格式
  nodes:
    - org_id: "wx-org1.chainmaker.org"
      address:
        - "/ip4/127.0.0.1/tcp/11301/p2p/QmcQHCuAXaFkbcsPUj7e37hXXfZ9DdN7bozseo5oX4qiC4"
    - org_id: "wx-org2.chainmaker.org"
      address:
        - "/ip4/127.0.0.1/tcp/11302/p2p/QmeyNRs2DwWjcHTpcVHoUSaDAAif4VQZ2wQDQAUNDP33gH"
    - org_id: "wx-org3.chainmaker.org"
      address:
        - "/ip4/127.0.0.1/tcp/11303/p2p/QmXf6mnQDBR9aHauRmViKzSuZgpumkn7x6rNxw1oqqRr45"
    - org_id: "wx-org4.chainmaker.org"
      address:
        - "/ip4/127.0.0.1/tcp/11304/p2p/QmRRWXJpAVdhFsFtd9ah5F4LDQWFFBDVKpECAF8hssqj6H"

# 信任组织和根证书
trust_roots:
  - org_id: "wx-org1.chainmaker.org"
    root: "./certs/ca/wx-org1.chainmaker.org/ca.crt"
  - org_id: "wx-org2.chainmaker.org"
    root: "./certs/ca/wx-org2.chainmaker.org/ca.crt"
  - org_id: "wx-org3.chainmaker.org"
    root: "./certs/ca/wx-org3.chainmaker.org/ca.crt"
  - org_id: "wx-org4.chainmaker.org"
    root: "./certs/ca/wx-org4.chainmaker.org/ca.crt"

# 权限配置（只能整体添加、修改、删除）
permissions:
  - resource_name: NODE_ADDR_UPDATE
    principle:
      rule: SELF # 规则（ANY，MAJORITY...，全部大写，自动转大写）
      org_list:  # 组织名称（组织名称，区分大小写）
      role_list: # 角色名称（role，全部小写，自动转小写）
        - admin
  - resource_name: TRUST_ROOT_UPDATE
    principle:
      rule: SELF # 规则（ANY，MAJORITY...，全部大写）
      org_list:  # 组织名称（组织名称）
      role_list: # 角色名称（role，全部小写）
        - admin
  - resource_name: CONSENSUS_EXT_DELETE
    principle:
      rule: MAJORITY
      org_list:
      role_list:
        - admin
  - resource_name: BLOCK_UPDATE
    principle:
      rule: ANY
      org_list:
      role_list:
        - admin
        - client
  - resource_name: INIT_CONTRACT
    principle:
      rule: ANY
      org_list:
      role_list:
  - resource_name: UPGRADE_CONTRACT
    principle:
      rule: ANY
      org_list:
      role_list:
  - resource_name: FREEZE_CONTRACT
    principle:
      rule: ANY
      org_list:
      role_list:
  - resource_name: UNFREEZE_CONTRACT
    principle:
      rule: ANY
      org_list:
      role_list:
  - resource_name: REVOKE_CONTRACT
    principle:
      rule: ANY
      org_list:
      role_list:
```

## 4 常用日志说明

### 4.1 正常日志

#### 4.1.1节点已连通

2021-02-03 15:29:01.016 [INFO]  [Net]   p2p/libp2p_connection_supervisor.go:85  [ConnSupervisor] all necessary peers connected.

#### 4.1.2 提案区块

2021-01-08 02:47:39.538 [DEBUG] [Consensus] @chain1 tbft/consensus_tbft_impl.go:403 [QmcQHCuAXaFkbcsPUj7e37hXXfZ9DdN7bozseo5oX4qiC4](1/0/Propose) receive proposal from core engine (1/), isProposer: true

#### 4.1.3 区块验证

2021-01-08 02:47:44.824 [DEBUG] [Consensus] @chain1 tbft/consensus_tbft_impl.go:614 [QmcQHCuAXaFkbcsPUj7e37hXXfZ9DdN7bozseo5oX4qiC4](2/0/Propose) send for verifying block: (2-312e6f853e6972bc4c5d7145729a22dc72ef5f6629fb376ef1b5c63a9dc8811c)

#### 4.1.4 区块落库

2021-01-08 02:47:39.716 [INFO] [Storage] @chain1 store/blockstore_impl.go:154 chain[chain1]: put block[1] (txs:1 bytes:506030), time used (mashal:1, log:7, commit:2, total:10)

#### 4.1.5 共识步骤

tbft进入proposal:

2021-01-08 02:47:35.824 [DEBUG] [Consensus] @chain1 tbft/consensus_tbft_impl.go:930 [QmcQHCuAXaFkbcsPUj7e37hXXfZ9DdN7bozseo5oX4qiC4] attempt enterPropose to (1/0)

tbft 进入prevote: 

2021-01-08 02:47:44.990 [INFO] [Consensus] @chain1 tbft/consensus_tbft_impl.go:977 [QmcQHCuAXaFkbcsPUj7e37hXXfZ9DdN7bozseo5oX4qiC4](2/0/Propose) enter prevote(2/0)

tbft 进入precommit: 2021-01-08 02:47:39.651 [INFO] [Consensus] @chain1 tbft/consensus_tbft_impl.go:1033 [QmcQHCuAXaFkbcsPUj7e37hXXfZ9DdN7bozseo5oX4qiC4](1/0/Prevote) enter precommit(1/0)

#### 4.1.6 合约执行

2021-02-03 15:35:19.218 [INFO]  [Vm] ^[[31;1m@chain1^[[0m       vm/vm_factory.go:554    invoke vm, tx id:f8460d3d738446ad898f94be38f0def612c5d92259154c93bdbfb4f71dc95197, tx type:MANAGE_USER_CONTRACT, contractId:contract_name:"asset_new28" contract_version:"1.0" runtime_type:WASMER , method:init_contract, runtime type:2, byte code len:272611, params:map[__block_height__:3 __creator_org_id__:wx-org1.chainmaker.org __creator_pk__:62c6a0672c28ae914e9c5100a2262762b0a5b7b13bf4b69b3beee92c51aefd0f __creator_role__:client __sender_org_id__:wx-org1.chainmaker.org __sender_pk__:62c6a0672c28ae914e9c5100a2262762b0a5b7b13bf4b69b3beee92c51aefd0f __sender_role__:client __tx_id__:f8460d3d738446ad898f94be38f0def612c5d92259154c93bdbfb4f71dc95197 issue_limit:1000 total_supply:100000000], sender:20b3ce617ba79010bba0bcdb212ab5bc21f32ed92d707b4a9ab891c3a124134e

2021-02-03 15:35:19.221 [DEBUG] [Vm] ^[[31;1m@chain1^[[0m       wasmer/vm_bridge.go:47  waci log>> [f8460d3d738446ad898f94be38f0def612c5d92259154c93bdbfb4f71dc95197] init: successed. Address["62c6a0672c28ae914e9c5100a2262762b0a5b7b13bf4b69b3beee92c51aefd0f"] . Total_supply is 100000000. Issue limit is 1000
2021-02-03 15:35:19.221 [DEBUG] [Vm] ^[[31;1m@chain1^[[0m       wasmer/runtime.go:27    wasmer runtime invoke[f8460d3d738446ad898f94be38f0def612c5d92259154c93bdbfb4f71dc95197]:used gas 29740112 , used time 3

#### 4.1.7 交易验证

2021-02-03 19:25:59.402 [DEBUG] [TxPool] ^[[31;1m@chain1^[[0m   txpool/tx_validator.go:26       validate tx success%!(EXTRA string=txId, string=2108e420fb3242e8a133621cd780fdba760f6427c3704de3a43f15669789a355)

### 4.2 错误日志

#### 4.2.1 交易签名错误

2021-02-03 19:33:53.712 [WARN]  [Access] ^[[31;1m@chain1^[[0m   idmgmt/organization.go:325      authentication failed, setup member failed, organization information in certificate and in input parameter do not match [certificate: wx-org6.chainmaker.org, parameter: wx-org1.chainmaker.org]

#### 4.2.2 权限错误

2021-02-03 19:40:05.102 [ERROR] [Core] ^[[31;1m@chain1^[[0m     proposer/tx_scheduler_impl.go:230       failed to run vm for tx id:3b2feb71081145eb9a062c1bc3b8ca456f52746aebb848dba1ab196805265bb6 during simulate with dag, tx result:code:INVALID_PARAMETER contract_result:<> , error:failed to verify endorsements for tx: 3b2feb71081145eb9a062c1bc3b8ca456f52746aebb848dba1ab196805265bb6, error: authentication fail: not enough partipants support this action

#### 4.2.3 合约执行失败

2021-02-03 16:21:46.599 [ERROR] [Core] ^[[31;1m@chain1^[[0m     proposer/tx_scheduler_impl.go:231       failed to run vm for tx id:da86bc1f88b34017b5d6ca56e52a7373d547efe28e18432abf346a43b31657ef during simulate with dag, tx result:code:CONTRACT_FAIL contract_result:<code:FAIL message:"[\"c5d7d472124c988175beacef2b482206910c94845777eb3689af33e240c67129\"] not registered" > , error:["c5d7d472124c988175beacef2b482206910c94845777eb3689af33e240c67129"] not registered

#### 4.2.4 合约安装失败

2021-02-03 16:15:19.064 [ERROR] [Core] ^[[31;1m@chain1^[[0m     proposer/tx_scheduler_impl.go:231       failed to run vm for tx id:2d7becc48eac4c2bb8b46ec3128f30351ee7acdf981e4c4fa9e566c654291128 during simulate with dag, tx result:code:FAILED_GET_FROM_TX_CONTEXT contract_result:<code:FAIL message:"verify fail, the contract already exists. contract:asset_new11, version:1.0" > , error:verify fail, the contract already exists. contract:asset_new11, version:1.0

#### 4.2.5 区块验证失败

### 4.3 故障日志

#### 4.3.1 启动错误

2021-02-03 16:50:54.267 [ERROR] [Cli]   cmd/cli_start.go:63     chainmaker server init failed, init blockchain[chain1] failed, PEM is nil

#### 4.3.2 节点无法建立连接

2021-02-03 16:52:40.150 [WARN]  [Net]   p2p/libp2p_connection_supervisor.go:93  [ConnSupervisor] try to connect to peer failed(peer:{QmQD1uNi8Fcyt69yJwwQYiRkN1etgwNzGG14Vg6Zx7Ym25: [/ip4/192.168.2.34/tcp/11307]}),dial backoff

## 5 状态码说明

### 5.1 交易状态码列表

```protobuf
// TxStatusCode describes the tx status in tx result
enum TxStatusCode {
    SUCCESS = 0;
    TIMEOUT = 1;
    INVALID_PARAMETER = 2;
    NO_PERMISSION = 3;
    CONTRACT_FAIL = 4;
    INTERNAL_ERROR = 5;

    INVALID_CONTRACT_TRANSACTION_TYPE = 10;
    INVALID_CONTRACT_PARAMETER_CONTRACT_NAME = 11;
    INVALID_CONTRACT_PARAMETER_METHOD = 12;
    INVALID_CONTRACT_PARAMETER_INIT_METHOD = 13;
    INVALID_CONTRACT_PARAMETER_UPGRADE_METHOD = 14;
    INVALID_CONTRACT_PARAMETER_BYTE_CODE = 15;
    INVALID_CONTRACT_PARAMETER_RUNTIME_TYPE = 16;
    INVALID_CONTRACT_PARAMETER_VERSION = 17;

    FAILED_GET_FROM_TX_CONTEXT = 20;
    FAILED_PUT_INTO_TX_CONTEXT = 21;
    FAILED_CONTRACT_VERSION_EXIST = 22;
    FAILED_CONTRACT_VERSION_NOT_EXIST = 23;
    FAILED_CONTRACT_BYTE_CODE_NOT_EXIST = 24;
    FAILED_MARSHAL_SENDER = 25;
    FAILED_INVOKE_INIT_METHOD = 26;
    FAILED_INVOKE_UPGRADE_METHOD = 27;
    FAILED_CREATE_RUNTIME_INSTANCE = 28;
    FAILED_UNMARSHAL_CREATOR = 29;
    FAILED_UNMARSHAL_SENDER = 30;
    FAILED_GET_SENDER_PK = 31;
    FAILED_GET_CREATOR_PK = 32;
    FAILED_GET_CREATOR = 33;
    FAILED_GET_CREATOR_CERT = 34;
    FAILED_GET_SENDER_CERT = 35;
    FAILED_CONTRACT_FREEZE = 36;
    FAILED_CONTRACT_TOO_DEEP = 37;
    FAILED_CONTRACT_REVOKE = 38;
    FAILED_CONTRACT_INVOKE_METHOD = 39;
}
```

### 5.2 合约状态码列表

```protobuf
// returned by contract
enum ContractResultCode {
    OK = 0;
    FAIL = 1;
}
```





