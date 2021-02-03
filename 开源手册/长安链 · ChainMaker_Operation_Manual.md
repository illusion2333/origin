# 长安链 · ChainMaker Operation Manual



## 脚本说明

脚本文件主要放在源码的tools和script路径下，分别说明如下：

cmc：ChainMaker命令行工具，一方面cmc可以通过rpc和节点进行通信，完成包括证书管理、合约管理、链配置管理、链信息查询等多项功能。另一方面也有本地的ca证书生成，用户和节点证书的签发等多项功能。

prepare.sh: 用来批量生成证书和节点配置。可以使用参数来指定节点数量和链数量

build_release.sh: 用来编译并打包完整的启动包，启动包内部包含了prepare生成的证书和配置文件，打包之后的文件放置在build/release下面，直接拷贝到需要部署的机器上解压修改监听地址后即可运行。也可以直接在本地调用cluster_quick_start.sh来快速启动多个节点。



## 部署路径说明

启动包解压后主要包含的目录说明如下：

config/wx-orgx.chainmaker.org: 配置文件路径，子目录说明如下：

- certs: 证书目录
- certs/ca: 存放所有组织根证书的目录，每个组织有一个以组织名称命名的文件夹
- certs/node/consensusx: 全节点身份证证的证书目录
- certs/node/commonx: 其他类型节点身份认证的证书目录
- certs/user/adminx: 节点管理员身份认证证书目录
- certs/user/clientx: 节点普通用户身份认证证书目录
- chainconfig: 链配置目录
- chainconfig/bcx.yml: 具体的链配置文件
- chainmaker.yml: 节点主配置文件
- log.yml: 日志配置文件

bin: 启动程序和脚本

- start.sh: 启动脚本
- restart.sh: 重启脚本
- stop.sh: 重启脚本

data: 存放数据库存储的目录

lib: 存储依赖库的目录

log: 日志路径

## 配置说明

【chainmaker.yml】

【bc*.yml】



## 常用日志说明

【正确日志及关键字：节点间已联通、提案区块、区块验证、区块落库、共识步骤、合约执行、交易验证】

【错误日志及关键字：交易签名错误、权限错误、合约执行失败、合约安装失败、区块验证失败等】

【故障日志及关键字：启动错误、节点无法建立连接等】





## 返回码说明









