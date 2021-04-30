# 长安链· ChainMaker Quick Start

## 1 快速部署

参考 [《ChainMaker Deploy Manual》](./ChainMaker_Deploy_Manual.md) 进行单节点、4节点快速部署

## 2 合约相关

本部署的实例参照了 [《ChainMaker Contract Manual》](./ChainMaker_Contract_Manual.md) 中“智能合约Sample”中RUST版转账合约。后续的方法调用和参数与该合约源代码对应。

### 2.1 创建合约

使用cmc工具创建一个合约，采用 chainmaker-go/test/wasm 的 asset-rust-0.7.2_v1.1.0.wasm 进行创建（该文件为已编译的智能合约字节码文件，前期合约开发、编译步骤可参见 [《ChainMaker Contract Manual》](./ChainMaker_Contract_Manual.md) ，此处省略），命令如下：

```sh
./cmc client contract user create \
      --admin-key-file-paths=./testdata/crypto-config/wx-org1.chainmaker.org/user/admin1/admin1.tls.key  \
      --admin-crt-file-paths=./testdata/crypto-config/wx-org1.chainmaker.org/user/admin1/admin1.tls.crt  \
      --org-id=wx-org1.chainmaker.org \
      --chain-id=chain1  \
      --client-crt-file-paths=./testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.crt \
      --client-key-file-paths=./testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.key \
      --byte-code-path=../../test/wasm/asset-rust-0.7.2_v1.1.0.wasm \
      --contract-name=asset_new24 \
      --runtime-type=WASMER \
      --sdk-conf-path=./testdata/sdk_config.yml \
      --version=1.0 \
      --sync-result=true \
      --params="{\"issue_limit\":\"500000000\",\"total_supply\":\"1000000000\"}"
```

cmc指令参数说明：

- create  创建合约
- admin-key-file-paths  管理员密钥路径
- admin-crt-file-paths  管理员证书路径
- org-id  组织id
- chain-id  节点id
- client-key-file-paths  客户端密钥路径
- client-crt-file-paths  客户端证书路径
- byte-code-path  合约字节码路径
- contract-name  合约名字
- runtime-type  合约运行时类型
- sdk-conf-path  sdk配置文件路径
- version  版本
- sync-resul  同步结果
- params  执行所需要参数，详见《ChainMaker_Contract_Manual》合约文档

正确输出如下：

```sh
create contract resp: message:"OK" 
contract_result:<result:"c8c088cd9e333950f47cd5f5e3d6ebdadf522459553da90c138ca8ce16549480" > 
```

注意：

1. cmc 在 chainmaker-go/tools/cmc 下

2. cmc 目录下如果没有cmc可执行文件，执行 go build

3. cmc 目录下如果没有 testdata 目录，执行：cp -r ../sdk/testdata ./ 

### 2.2 调用合约

调用 contract-name=asset_new24 的合约中的  total_supply 方法，查看当前合约总金额，命令如下：

```sh
./cmc client contract user invoke \
			--contract-name=asset_new24 \
			--method=total_supply \
			--org-id=wx-org1.chainmaker.org \
			--client-crt-file-paths=./testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.crt \
			--client-key-file-paths=./testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.key \
			--sdk-conf-path=./testdata/sdk_config.yml \
			--sync-result=true
```

cmc指令参数说明：

- invoke  合约调用
- contract-name  合约名字
- method  执行方法
- org-id  组织id
- client-crt-file-paths  客户端key路径
- client-key-file-paths  客户端证书路径
- sdk-conf-path  sdk配置文件路径
- sync-result  同步结果

正确输出如下：

```sh
INVOKE contract resp, [code:0]/[msg:OK]/[contractResult:result:"1000000000" ]
```

### 2.3 查询合约

查询 contract-name=asset_new24 的合约下自己的钱包地址，命令如下：

```sh
./cmc client contract user get \
			--contract-name=asset_new24 \
			--method=query_address \
			--sdk-conf-path=./testdata/sdk_config.yml \
			--org-id=wx-org1.chainmaker.org \
			--client-crt-file-paths=./testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.crt \
			--client-key-file-paths=./testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.key
```

cmc指令参数说明：

- get  查询合约
- contract-name  合约名字
- method  执行方法
- sdk-conf-path  sdk配置文件路径
- org-id  组织id
- client-crt-file-paths  客户端key路径
- client-key-file-paths  客户端证书路径

正确输出如下：

```sh
QUERY contract resp: message:"SUCCESS" contract_result
<result:"c8c088cd9e333950f47cd5f5e3d6ebdadf522459553da90c138ca8ce16549480" >
```

## 3 链相关

### 3.1 查询链信息

查询区块链信息，命令如下：

```sh
./cmc client contract system getchaininfo \
			--org-id=wx-org1.chainmaker.org \
			--client-crt-file-paths=./testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.crt \
			--client-key-file-paths=./testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.key \
			--sdk-conf-path=./testdata/sdk_config.yml
```

cmc指令参数说明：

- getchaininfo  查询链信息
- org-id  组织id
- client-crt-file-paths  客户端key路径
- client-key-file-paths  客户端证书路径
- sdk-conf-path  sdk配置文件路径

正确输出如下：

```sh
get chain info resp: block_height:30 node_list:<node_id:"QmRZBFSKT6SRkdkVo3Hbaz9gcBLHP5XFgBHdEhXkasjZjH"
node_address:"/ip4/192.168.1.95/tcp/11301,/ip4/127.0.0.1/tcp/11301" 
node_tls_cert:"0\202\003\0260\202\002\273\240\003\002\001\002\002\003\004,30\n\006\010*\201\034\317U\001\203u0\201\2121\0130\t\006\003U\004\006\023\002CN1\0200\016\006\003U\004\010\023\007Beijing1\0200\016\006\003U\004\007\023\007Beijing1\0370\035\006\003U\004\n\023\026wx-org1.chainmaker.org1\0220\020\006\003U\004\013\023\troot-cert1\"0 \006\003U\004\003\023\031ca.wx-org1.chainmaker.org0\036\027\r210309063236Z\027\r260308063236Z0\201\2261\0130\t\006\003U\004\006\023\002CN1\0200\016\006\003U\004\010\023\007Beijing1\0200\016\006\003U\004\007\023\007Beijing1\0370\035\006\003U\004\n\023\026wx-org1.chainmaker.org1\0220\020\006\003U\004\013\023\tconsensus1.0,\006\003U\004\003\023%consensus1.tls.wx-org1.chainmaker.org0Y0\023\006\007*\206H\316=\002\001\006\010*\201\034\317U\001\202-\003B\000\004\017Vt:$\277\375\023\354\231\223\352\025\251/m\223\355\223\037\266\2244m)O\022\230\t=\320&\234\272J\372\364M\330B\201:\262w;\355\316\265\027\326#_\3265\223\243\022\315\261-z\017\251\032\243\202\001\0000\201\3750\016\006\003U\035\017\001\001\377\004\004\003\002\001\2460\017\006\003U\035%\004\0100\006\006\004U\035%\0000)\006\003U\035\016\004\"\004 A\347\202\003hy\363\302\234\277nM\365\265^\325f\356Z\270\365px\\\200\0077\363\331\203\222P0+\006\003U\035#\004$0\"\200 \364Gw@\274\376\223\033oe\261:\"\304:\3256 /\266\355\\\003x!B\rF\003\315\215\3530Q\006\003U\035\021\004J0H\202\016chainmaker.org\202\tlocalhost\202%consensus1.tls.wx-org1.chainmaker.org\207\004\177\000\000\0010/\006\013\201'X\217d\013\036\217d\013\004\004 12f607859a4f4ada84dfca2f8c563e6b0\n\006\010*\201\034\317U\001\203u\003I\0000F\002!\000\357:\244\017-\006\232=\376)\232\324[5(\330\353\013\201\375\344]\245}\235\371\256nB\324,\305\002!\000\222\037]`I\350Fe`\306V}q\204\264\025\201\007\272o\376\357I\251\n\302\236\270\216\342\\F" >
```

注意：

1. 单节点没有启动网络模块，所以会出现以下错误

```sh
Error: get chain info failed, QUERY_SYSTEM_CONTRACT failed, 4, 1, 
get ChainNodesInfoProvider error: chainNodesInfoProvider for tx sim context is nil
```

### 3.2 查询区块信息（根据高度）

查询区块高度为 1 的区块，命令如下：

```sh
./cmc client contract system block \
    --sdk-conf-path ./testdata/sdk_config.yml \
    --block-height 1 \
    --with-rw-set true
```

cmc指令参数说明：

- block  获取区块信息
- block-height  区块高度
- sdk-conf-path  sdk配置文件路径

正确输出如下：

````sh
get block by height resp: block:<header:<chain_id:"chain1" block_height:1 
pre_block_hash:"\334\341\302j\300\010\022X\343\206\21055r\0003\221awl!\030\003\016[i\"\324G2Z+" 
block_hash:"\257\030lj\371\317DF?\020\375U\313=\\\367\357\220\225\032\276e\027v\250\354\0059%|\ro" block_version:"v1.0.0" 
dag_hash:"\010\332|E\313 Cw\347\344\"I\315\245q?\250e\021m\333\264\313Z\031I\262\345\2648\246\253" 
rw_set_root:"f\300\276\316\346\355\350\200\033\351P\"dy\373\261\345\333=\261\034\272E\270\367:\235\251\261\025\177O" 
tx_root:"n\005D\033\305q\003[(\315K0\276u\345\255\013\316\005\004\357\271\231\305\310\203T\211\305\023\340\230"
````

### 3.3 查询交易信息（根据交易id）

查询 交易id 为 61cef77f82df47fd9278d365c6261f5707a69b99b45e437bb611627ef2144ed7 的交易，命令如下：

```sh
./cmc client contract system tx \
    --sdk-conf-path ./testdata/sdk_config.yml \
    --tx-id 61cef77f82df47fd9278d365c6261f5707a69b99b45e437bb611627ef2144ed7
```

cmc指令参数说明：

- tx  获取交易信息
- sdk-conf-path  sdk配置文件路径
- tx-id  交易id

正确输出如下：

```sh
result:<contract_result:<result:"c8c088cd9e333950f47cd5f5e3d6ebdadf522459553da90c138ca8ce16549480" > 
rw_set_hash:"\253\265\334{\035h\370xp\231\255M\216\203M\037tt\331_\224y\350\334\177J%\262\rZ\375\333" > > block_height:13
```

