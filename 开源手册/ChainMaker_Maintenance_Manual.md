# 长安链 · ChainMaker Maintenance Manual

## 1 链上配置变更

链上配置更新当前可以通过cmc工具进行，更新出块间隔示例如下：

```sh
./cmc client chainconfig block updateblockinterval --org-id=wx-org1.chainmaker.org --client-crt-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.crt --client-key-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.key --sdk-conf-path=../sdk/testdata/sdk_config.yml --admin-crt-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/admin1/admin1.tls.crt --admin-key-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/admin1/admin1.tls.key --block-interval 1000
```

## 2 增加新链

### 2.1 添加链创世块配置，参考[《长安链 · ChainMaker_Operation_manual》](./ChainMaker_Operation_Manual.md)

### 2.2 修改chainmaker配置文件

在chainmaker.yml的头部将新链的Id和创世块配置添加进去，如下：
```yaml
blockchain:
  - chainId: chain1
      genesis: chainconfig/bc1.yml
```

### 2.3 触发chainmaker初始化新链

使用cmc工具触发chainmaker初始化新链，命令如下：

```sh
./cmc client blockchains checknew --sdk-conf-path=../sdk/testdata/sdk_config.yml
```

## 3 添加共识节点

新增共识节点时，如果节点组织的根证书已经配置在链上，则可以直接执行3.2添加证书，否则需要先执行3.1添加组织的CA证书。

### 3.1 添加CA证书

使用cmc工具添加CA证书，命令如下：

```sh
./cmc client chainconfig trustroot add --admin-key-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/admin1/admin1.tls.key --admin-crt-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/admin1/admin1.tls.crt  --org-id=wx-org1.chainmaker.org --client-crt-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.crt --client-key-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.key --sdk-conf-path=../sdk/testdata/sdk_config.yml --trust-root-org-id=wx-org6.chainmaker.org --trust-root-path=../sdk/testdata/crypto-config/wx-org6.chainmaker.org/ca/ca.crt
```

### 3.2 添加共识节点

使用cmc工具添加共识节点，命令如下：

```sh
./cmc client chainconfig consensusnode add --admin-key-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/admin1/admin1.tls.key --admin-crt-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/admin1/admin1.tls.crt  --org-id=wx-org1.chainmaker.org --client-crt-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.crt --client-key-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.key --sdk-conf-path=../sdk/testdata/sdk_config.yml --node-org-id=wx-org6.chainmaker.org --node-address=/ip4/192.168.2.33/tcp/11306/p2p/QmaidqyzfPv6y97JF9ZcrG7q3mgU9ALaQmMDUTajiCeXHe
```

## 4 移除共识节点

使用cmc工具移除共识节点，命令如下：

```sh
./cmc client chainconfig consensusnode remove --admin-key-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/admin1/admin1.tls.key --admin-crt-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/admin1/admin1.tls.crt  --org-id=wx-org1.chainmaker.org --client-crt-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.crt --client-key-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.key --sdk-conf-path=../sdk/testdata/sdk_config.yml --node-org-id=wx-org6.chainmaker.org --node-address=/ip4/192.168.2.33/tcp/11306/p2p/QmaidqyzfPv6y97JF9ZcrG7q3mgU9ALaQmMDUTajiCeXHe
```

## 5 添加同步节点

添加新同步节点只需将组织的CA证书添加到链上即可，参考3.1

## 6 移除同步节点

移除同步节点，直接将同步节点停止即可，无需链上操作