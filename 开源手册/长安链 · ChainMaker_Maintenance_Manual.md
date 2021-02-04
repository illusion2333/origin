# 长安链 · ChainMaker Maintenance Manual

## 1 链上配置变更

链上配置更新当前可以通过cmc工具进行，更新出块间隔示例如下：

```sh
./cmc client chainconfig block updateblockinterval --org-id=wx-org1.chainmaker.org --client-crt-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.crt --client-key-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.key --sdk-conf-path=../sdk/testdata/sdk_config.yml --admin-crt-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/admin1/admin1.tls.crt --admin-key-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/admin1/admin1.tls.key --block-interval 1000
```



## 2 增加新链



## 3 新共识节点加入

新增共识节点时，如果节点组织的根证书已经配置在链上，则可以直接执行3.2添加证书，否则需要先执行3.1添加组织的CA证书。

### 3.1 添加CA证书

```sh
./cmc client chainconfig trustroot add --admin-key-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/admin1/admin1.tls.key --admin-crt-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/admin1/admin1.tls.crt  --org-id=wx-org1.chainmaker.org --client-crt-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.crt --client-key-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.key --sdk-conf-path=../sdk/testdata/sdk_config.yml --trust-root-org-id=wx-org6.chainmaker.org --trust-root-path=../sdk/testdata/crypto-config/wx-org6.chainmaker.org/ca/ca.crt
```

### 3.2 添加共识节点

```sh
./cmc client chainconfig consensusnode add --admin-key-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/admin1/admin1.tls.key --admin-crt-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/admin1/admin1.tls.crt  --org-id=wx-org1.chainmaker.org --client-crt-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.crt --client-key-file-paths=../sdk/testdata/crypto-config/wx-org1.chainmaker.org/user/client1/client1.tls.key --sdk-conf-path=../sdk/testdata/sdk_config.yml --node-org-id=wx-org6.chainmaker.org --node-address=/ip4/192.168.2.33/tcp/11306/p2p/QmaidqyzfPv6y97JF9ZcrG7q3mgU9ALaQmMDUTajiCeXHe
```



## 4 新同步节点加入

添加新同步节点只需将

## 5 节点剔除















