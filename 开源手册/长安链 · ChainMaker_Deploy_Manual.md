# 长安链 · ChainMaker Deploy Manual

@永芯

## 1 依赖环境



## 2 部署方式

### 本地部署

【简述，同步源码、下载可执行文件】

### docker

【简述，我们提供的镜像】

### ~~K8S~~





## 3 快速部署（SOLO模式）

### 3.1 物料下载

```sh
git clone --recurse-submodules git@git.code.tencent.com:ChainMaker/chainmaker-go.git
```

### 3.2 编译

```sh
cd chainmaker-go
make pb-dep
make
```

### 3.3 配置设置

修改链配置文件（路径为chainmaker-V1.0.0-wx-org1.chainmaker.org/config/wx-org1.chainmaker.org/chainconfig/bc1.yml）配置项consensus: type值为6（solo模式）

```sh
cd build/release

tar zvxf chainmaker-V1.0.0-wx-org1.chainmaker.org-20201201204232-x86_64.tar.gz


```

### 3.4 启动



```sh
cd chainmaker-V1.0.0-wx-org1.chainmaker.org/bin
./start.sh
```

### 3.5 交易验证

```sh
./cmc client contract user create --admin-key-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/admin1/admin1.tls.key --admin-crt-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/admin1/admin1.tls.crt  --org-id=wx-org1.chainmaker.org --chain-id=chain1 --client-crt-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.crt --client-key-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.key --byte-code-path=../../test/wasm/asset-rust-0.7.2.wasm --contract-name=asset_new24 --runtime-type=WASMER --sdk-conf-path=../sdk/testdata/sdk_config.yml --version=1.0 --sync-result=true --params="{\"issue_limit\":\"500000000\",\"total_supply\":\"1000000000\"}"
```

## 4 快速部署（4节点TBFT共识）

### 4.1 物料下载

#### 4.1.1 下载源码

git clone --recurse-submodules git@git.code.tencent.com:ChainMaker/chainmaker-go.git

#### 4.1.2 安装tmux

Mac: brew install tmux

Centos: yum install tmux

### 4.2 编译

```sh
cd chainmaker-go
make pb-dep
make
```

### 4.3 启动

```sh
cd scripts
./cluster_quick_start.sh
```

### 4.4 交易验证

```sh
./cmc client contract user create --admin-key-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/admin1/admin1.tls.key --admin-crt-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/admin1/admin1.tls.crt  --org-id=wx-org1.chainmaker.org --chain-id=chain1 --client-crt-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.crt --client-key-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.key --byte-code-path=../../test/wasm/asset-rust-0.7.2.wasm --contract-name=asset_new24 --runtime-type=WASMER --sdk-conf-path=../sdk/testdata/sdk_config.yml --version=1.0 --sync-result=true --params="{\"issue_limit\":\"500000000\",\"total_supply\":\"1000000000\"}"
```

















