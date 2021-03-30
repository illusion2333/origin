# 长安链 · ChainMaker Tools Manual

## 周边工具

### 命令行工具CMC

cmc是一个命令行工具集，主要包括长安链节点管理（使用sdk和长安链之间通过rpc交互实现）、各类证书生成等功能，可以通过help来查看命令的用法。更多使用示例参考：[《长安链 ChainMaker_Deploy_Manual》](./ChainMaker_Deploy_Manual.md)和[《长安链 ChainMaker_Maintenance_Manual》](./ChainMaker_Maintenance_Manual.md)

### SDK

请参考：[《chainmaker-go-sdk 》](./chainmaker-go-sdk.md)、[《chainmaker-java-sdk》](./chainmaker-java-sdk.md)

### cryptogen

#### 工具说明

`chainmaker-cryptogen`是基于配置文件生成`ChainMaker`节点和客户端证书的工具，方便在没有`CA`的情况下，进行开发和测试。

#### 工具配置

```yml
crypto_config:
  - domain: chainmaker.org
    host_name: wx-org
    count: 4                # 如果为1，直接使用host_name，否则添加递增编号
    #pk_algo: ecc_p256
    pk_algo: sm2
    ski_hash: sha256
    specs: &specs_ref
      expire_year:  10
      sans:
        - chainmaker.org
        - localhost
        - 127.0.0.1
    location: &location_ref
      country:            CN
      locality:           Beijing
      province:           Beijing
    # CA证书配置
    ca:
      location:
        <<: *location_ref
      specs:
        <<: *specs_ref
    # 节点证书配置
    node:
      - type: consensus
        # 共识节点数量
        count: 1
        # 共识节点配置
        location:
          <<: *location_ref
        specs:
          <<: *specs_ref
          expire_year:  5
      - type: common
        # 普通节点数量
        count: 1
        # 普通节点配置
        location:
          <<: *location_ref
        specs:
          <<: *specs_ref
          expire_year:  5
    user:
      - type: admin
        # 管理员证书数量
        count: 1
        # 管理员证书配置
        location:
          <<: *location_ref
        expire_year:  5
      - type: client
        # 普通用户证书数量
        count: 1
        # 普通用户证书配置
        location:
          <<: *location_ref
        expire_year:  5
```

#### <span id="cryptoUsed">使用方法</span>

- 命令帮助

```bash
$ ./chainmaker-cryptogen -h
Usage:
  chainmaker-cryptogen [command]

Available Commands:
  extend      Extend existing network
  generate    Generate key material
  help        Help about any command
  showconfig  Show config

Flags:
  -c, --config string   specify config file path (default "../config/crypto_config_template.yml")
  -h, --help            help for chainmaker-cryptogen

Use "chainmaker-cryptogen [command] --help" for more information about a command.
```

- 生成证书

```bash
$ ./chainmaker-cryptogen generate

$ tree -L 3 crypto-config/
crypto-config/
├── wx-org1.chainmaker.org
│   ├── ca
│   │   ├── ca.crt
│   │   └── ca.key
│   ├── node
│   │   ├── common1
│   │   └── consensus1
│   └── user
│       ├── admin1
│       └── client1
├── wx-org2.chainmaker.org
│   ├── ca
│   │   ├── ca.crt
│   │   └── ca.key
│   ├── node
│   │   ├── common1
│   │   └── consensus1
│   └── user
│       ├── admin1
│       └── client1
├── wx-org3.chainmaker.org
│   ├── ca
│   │   ├── ca.crt
│   │   └── ca.key
│   ├── node
│   │   ├── common1
│   │   └── consensus1
│   └── user
│       ├── admin1
│       └── client1
└── wx-org4.chainmaker.org
    ├── ca
    │   ├── ca.crt
    │   └── ca.key
    ├── node
    │   ├── common1
    │   └── consensus1
    └── user
        ├── admin1
        └── client1
```

- 证书目录结构

![image-20210205145640521](/images/ca-structure.png)

```
$ tree crypto-config/wx-org1.chainmaker.org/
crypto-config/wx-org1.chainmaker.org/
├── ca
│   ├── ca.crt
│   └── ca.key
├── node
│   ├── common1
│   │   ├── common1.nodeid
│   │   ├── common1.sign.crt
│   │   ├── common1.sign.key
│   │   ├── common1.tls.crt
│   │   └── common1.tls.key
│   └── consensus1
│       ├── consensus1.nodeid
│       ├── consensus1.sign.crt
│       ├── consensus1.sign.key
│       ├── consensus1.tls.crt
│       └── consensus1.tls.key
└── user
    ├── admin1
    │   ├── admin1.sign.crt
    │   ├── admin1.sign.key
    │   ├── admin1.tls.crt
    │   └── admin1.tls.key
    └── client1
        ├── client1.sign.crt
        ├── client1.sign.key
        ├── client1.tls.crt
        └── client1.tls.key
```

### 在线IDE





















