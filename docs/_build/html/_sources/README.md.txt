# ChainMaker

长安链·ChainMaker区块链底层平台是新一代区块链开源底层软件平台，包含区块链核心框架、丰富的组件库和工具集，致力于为用户高效、精准地解决差异化区块链实现需求，构建高性能、高可信、高安全的新型数字基础设施。



## 用户手册、开发手册、快速指南、运维手册等

点击连接访问长安链的文档，获取详细的入门、开发、部署、运维管理等信息：

长安链概述：

- [ChainMaker - 概述](./ChainMaker_General_Introduction.md)

快速指南：

- [ChainMaker - Quick Start](./ChainMaker_Quick_Start.md)

开发类：

- [ChainMaker - 用户手册](./ChainMaker_User_Manual.md)
- [ChainMaker - 数据结构](./ChainMaker_Data_Structure.md)
- [ChainMaker - Go-SDK](ChainMaker_SDK_Go_Manual.md)
- [ChainMaker - Java-SDK](ChainMaker_SDK_Java_Manual.md)
- [ChainMaker - 工具链](./ChainMaker_Tools_Manual.md)

智能合约：

- [ChainMaker - 智能合约手册](./ChainMaker_Contract_Manual.md)
- [ChainMaker - 智能合约 - Go开发手册](ChainMaker_Contract_Programing_Go.md)
- [ChainMaker - 智能合约 - C++开发手册](ChainMaker_Contract_Programing_C++.md)
- [ChainMaker - 智能合约 - Rust开发手册](ChainMaker_Contract_Programing_Rust.md)
- [ChainMaker - 智能合约预编译依赖](./ChainMaker_Contract_Compile_Dependence.md)

运维类：

- [ChainMaker - 部署手册](./ChainMaker_Deploy_Manual.md)
- [ChainMaker - 操作手册](./ChainMaker_Operation_Manual.md)
- [ChainMaker - 运维手册](./ChainMaker_Maintenance_Manual.md)
- [ChainMaker - pprof性能分析工具](ChainMaker_PPROF_Manual.md)




## 手册大纲

点击连接访问长安链的文档，获取详细的入门、开发、部署、运维管理等信息：

**长安链概述：**

- [ChainMaker - 概述](./ChainMaker_General_Introduction.md)
  - [什么是长安链](./ChainMaker_General_Introduction.md#user-content-什么是长安链)
  - [长安链主要特性](./ChainMaker_General_Introduction.md#user-content-长安链主要特性)
  - [使用长安链可以做什么](./ChainMaker_General_Introduction.md#user-content-使用长安链可以做什么)

快速指南：

- [ChainMaker - Quick Start](./ChainMaker_Quick_Start.md)
  - [1 快速部署](./ChainMaker_Quick_Start.md#user-content-1-快速部署)
  - [2 合约相关](./ChainMaker_Quick_Start.md#user-content-2-合约相关)
    - [2.1 创建合约](./ChainMaker_Quick_Start.md#user-content-21-创建合约)
    - [2.2 调用合约](./ChainMaker_Quick_Start.md#user-content-22-调用合约)
    - [2.3 查询合约](./ChainMaker_Quick_Start.md#user-content-23-查询合约)
  - [3 链相关](./ChainMaker_Quick_Start.md#user-content-3-链相关)
    - [3.1 查询链信息](./ChainMaker_Quick_Start.md#user-content-31-查询链信息)
    - [3.2 查询区块信息根据高度](./ChainMaker_Quick_Start.md#user-content-32-查询区块信息根据高度)
    - [3.3 查询交易信息根据交易id](./ChainMaker_Quick_Start.md#user-content-33-查询交易信息根据交易id)

**开发类：**

用户手册

- [ChainMaker - 用户手册](./ChainMaker_User_Manual.md)
  - [整体架构](./ChainMaker_User_Manual.md#user-content-整体架构)
  - [核心流程](./ChainMaker_User_Manual.md#user-content-核心流程)
  - [核心特性](./ChainMaker_User_Manual.md#user-content-核心特性)
  - [模块说明](./ChainMaker_User_Manual.md#user-content-模块说明)
    - [智能合约](./ChainMaker_User_Manual.md#user-content-智能合约)
    - [系统合约](./ChainMaker_User_Manual.md#user-content-系统合约)
    - [合约SDK](./ChainMaker_User_Manual.md#user-content-合约sdk)
    - [合约模块接口说明](./ChainMaker_User_Manual.md#user-content-合约模块接口说明)
    - [PB数据模型](./ChainMaker_User_Manual.md#user-content-PB数据模型)
  - [共识算法](./ChainMaker_User_Manual.md#user-content-共识算法)
    - [TBFT](./ChainMaker_User_Manual.md#user-content-tbft)
    - [SOLO](./ChainMaker_User_Manual.md#user-content-solo)
  - [P2P网络](./ChainMaker_User_Manual.md#user-content-p2p网络)
    - [组网方式](./ChainMaker_User_Manual.md#user-content-组网方式)
    - [节点身份管理方式](./ChainMaker_User_Manual.md#user-content-节点身份管理方式)
    - [模块接口](./ChainMaker_User_Manual.md#user-content-模块接口)
    - [使用配置](./ChainMaker_User_Manual.md#user-content-使用配置)
    - [节点地址格式说明](./ChainMaker_User_Manual.md#user-content-节点地址格式说明)
    - [网络消息数据格式说明加密前](./ChainMaker_User_Manual.md#user-content-网络消息数据格式说明加密前)
  - [RPC服务](./ChainMaker_User_Manual.md#user-content-rpc服务)
    - [配置说明](./ChainMaker_User_Manual.md#user-content-配置说明)
    - [接口定义](./ChainMaker_User_Manual.md#user-content-接口定义)
    - [关键数据结构](./ChainMaker_User_Manual.md#user-content-关键数据结构)
    - [关键逻辑](./ChainMaker_User_Manual.md#user-content-关键逻辑)
  - [存储模块](./ChainMaker_User_Manual.md#user-content-存储模块)
    - [账本存储的处理流程](./ChainMaker_User_Manual.md#user-content-账本存储的处理流程)
    - [账本数据库类型](./ChainMaker_User_Manual.md#user-content-账本数据库类型)
    - [配置说明](./ChainMaker_User_Manual.md#user-content-配置说明)
    - [RocksDB部署](./ChainMaker_User_Manual.md#user-content-rocksdb部署)
    - [MySQL存储](./ChainMaker_User_Manual.md#user-content-mysql存储)
  - [身份与权限管理](./ChainMaker_User_Manual.md#user-content-身份与权限管理)
    - [成员member](./ChainMaker_User_Manual.md#user-content-成员member)
    - [组织organization](./ChainMaker_User_Manual.md#user-content-组织organization)
    - [组织-成员对应关系图](./ChainMaker_User_Manual.md#user-content-组织-成员对应关系图)
  - [权限管理模块](./ChainMaker_User_Manual.md#user-content-权限管理模块)
    - [权限规则](./ChainMaker_User_Manual.md#user-content-权限规则)
    - [身份-权限策略对](./ChainMaker_User_Manual.md#user-content-身份-权限策略对)
    - [接口使用说明](./ChainMaker_User_Manual.md#user-content-接口使用说明)
    - [新增资源](./ChainMaker_User_Manual.md#user-content-新增资源)
  - [配置模块](./ChainMaker_User_Manual.md#user-content-配置模块)
    - [本地配置](./ChainMaker_User_Manual.md#user-content-本地配置)
    - [链配置](./ChainMaker_User_Manual.md#user-content-链配置)
    - [配置变更](./ChainMaker_User_Manual.md#user-content-配置变更)
    - [网络消息](./ChainMaker_User_Manual.md#user-content-网络消息)
  - [轻节点模块](./ChainMaker_User_Manual.md#user-content-轻节点模块)
    - [同步逻辑](./ChainMaker_User_Manual.md#user-content-同步逻辑)
    - [配置说明](./ChainMaker_User_Manual.md#user-content-配置说明)
  - [交易池](./ChainMaker_User_Manual.md#user-content-交易池)
  - [组件描述](./ChainMaker_User_Manual.md#user-content-组件描述)
  - [加密算法](./ChainMaker_User_Manual.md#user-content-加密算法)
  - [核心引擎](./ChainMaker_User_Manual.md#user-content-核心引擎)
    - [流程说明](./ChainMaker_User_Manual.md#user-content-流程说明)
    - [配置说明](./ChainMaker_User_Manual.md#user-content-配置说明)
  - [日志](./ChainMaker_User_Manual.md#user-content-日志)
    - [如何使用](./ChainMaker_User_Manual.md#user-content-如何使用)
    - [logger配置](./ChainMaker_User_Manual.md#user-content-logger配置)

数据结构

- [ChainMaker - 数据结构](./ChainMaker_Data_Structure.md)
  - [区块](./ChainMaker_Data_Structure.md#user-content-区块)
    - [整体结构](./ChainMaker_Data_Structure.md#user-content-整体结构)
    - [区块头](./ChainMaker_Data_Structure.md#user-content-区块头)
    - [交易结构](./ChainMaker_Data_Structure.md#user-content-交易结构)
    - [交易头](./ChainMaker_Data_Structure.md#user-content-交易头)
    - [交易结果](./ChainMaker_Data_Structure.md#user-content-交易结果)
    - [交易请求结构](./ChainMaker_Data_Structure.md#user-content-交易请求结构)
    - [交易响应结构](./ChainMaker_Data_Structure.md#user-content-交易响应结构)

Go-SDK

- [ChainMaker - Go-SDK](ChainMaker_SDK_Go_Manual.md)
  - [下载安装](./ChainMaker_SDK_Go_Manual.md#user-content-下载安装)
  - [使用示例](./ChainMaker_SDK_Go_Manual.md#user-content-使用示例)
  - [接口说明](./ChainMaker_SDK_Go_Manual.md#user-content-接口说明)
    - [用户合约接口](./ChainMaker_SDK_Go_Manual.md#user-content-用户合约接口)
    - [系统合约接口](./ChainMaker_SDK_Go_Manual.md#user-content-系统合约接口)
    - [链配置接口](./ChainMaker_SDK_Go_Manual.md#user-content-链配置接口)
    - [证书管理接口](./ChainMaker_SDK_Go_Manual.md#user-content-证书管理接口)
    - [消息订阅接口](./ChainMaker_SDK_Go_Manual.md#user-content-消息订阅接口)
    - [证书压缩](./ChainMaker_SDK_Go_Manual.md#user-content-证书压缩)

 Java-SDK

- [ChainMaker - Java-SDK](ChainMaker_SDK_Java_Manual.md)
  - [基本概念定义](./ChainMaker_SDK_Java_Manual.md#user-content-1-基本概念定义)
  - [ChainClient类接口定义](./ChainMaker_SDK_Java_Manual.md#user-content-2-chainclient类接口定义)
    - [用户合约接口](./ChainMaker_SDK_Java_Manual.md#user-content-21-用户合约接口)
    - [系统合约接口](./ChainMaker_SDK_Java_Manual.md#user-content-22-系统合约接口)
    - [链配置接口](./ChainMaker_SDK_Java_Manual.md#user-content-23-链配置接口)
    - [证书管理接口](./ChainMaker_SDK_Java_Manual.md#user-content-24-证书管理接口)
    - [多签接口](./ChainMaker_SDK_Java_Manual.md#user-content-25-多签接口)
    - [消息订阅接口](./ChainMaker_SDK_Java_Manual.md#user-content-26-消息订阅接口)
    - [管理类接口](./ChainMaker_SDK_Java_Manual.md#user-content-27-管理类接口)
  - [User类接口](./ChainMaker_SDK_Java_Manual.md#user-content-3-user类接口)
  - [使用过程](./ChainMaker_SDK_Java_Manual.md#user-content-4-使用过程)
  - [使用示例](./ChainMaker_SDK_Java_Manual.md#user-content-5-使用示例)
  - [SDK Jar包引用方式](./ChainMaker_SDK_Java_Manual.md#user-content-6-sdk-jar包引用方式)

工具链

- [ChainMaker - 工具链](./ChainMaker_Tools_Manual.md)
  - [命令行工具CMC](./ChainMaker_Tools_Manual.md#user-content-命令行工具cmc)
  - [工具配置](./ChainMaker_Tools_Manual.md#user-content-工具配置)
  - [使用方法](./ChainMaker_Tools_Manual.md#user-content-使用方法)
  



**智能合约：**

智能合约手册

- [ChainMaker - 智能合约手册](./ChainMaker_Contract_Manual.md)
  - [智能合约生命周期管理](./ChainMaker_Contract_Manual.md#user-content-智能合约生命周期管理)
    - [合约部署](./ChainMaker_Contract_Manual.md#user-content-合约部署)
    - [合约升级](./ChainMaker_Contract_Manual.md#user-content-合约升级)
    - [合约冻结](./ChainMaker_Contract_Manual.md#user-content-合约冻结)
    - [合约解冻](./ChainMaker_Contract_Manual.md#user-content-合约解冻)
    - [合约注销](./ChainMaker_Contract_Manual.md#user-content-合约注销)
  - [智能合约Sample](./ChainMaker_Contract_Manual.md#user-content-智能合约sample)
    - [存证](./ChainMaker_Contract_Manual.md#user-content-存证)
    - [转账](./ChainMaker_Contract_Manual.md#user-content-转账)
  - [编译智能合约](./ChainMaker_Contract_Manual.md#user-content-编译智能合约)
  - [约束条件和已知问题](./ChainMaker_Contract_Manual.md#user-content-约束条件和已知问题)
  

Go开发手册

- [ChainMaker - 智能合约 - Go开发手册](ChainMaker_Contract_Programing_Go.md)
  - [合约编写流程](./ChainMaker_Contract_Programing_Go.md#user-content-1-合约编写流程)
    - [示例代码说明](./ChainMaker_Contract_Programing_Go.md#user-content-12-示例代码说明)
    - [代码编写规则](./ChainMaker_Contract_Programing_Go.md#user-content-13-代码编写规则)
    - [编译说明](./ChainMaker_Contract_Programing_Go.md#user-content-14-编译说明)
  - [合约发布过程](./ChainMaker_Contract_Programing_Go.md#user-content-2-合约发布过程)
  - [合约调用过程](./ChainMaker_Contract_Programing_Go.md#user-content-3-合约调用过程)
  - [Go SDK API描述](./ChainMaker_Contract_Programing_Go.md#user-content-4-go-sdk-api描述)
  

C++开发手册

- [ChainMaker - 智能合约 - C++开发手册](ChainMaker_Contract_Programing_C++.md)
  - [合约编写流程](./ChainMaker_Contract_Programing_C++.md#user-content-1-合约编写流程)
    - [示例代码说明](./ChainMaker_Contract_Programing_C++.md#user-content-13-示例代码说明)
    - [代码编写规则](./ChainMaker_Contract_Programing_C++.md#user-content-14-代码编写规则)
    - [编译说明](./ChainMaker_Contract_Programing_C++.md#user-content-15-编译说明)
  - [合约发布过程](./ChainMaker_Contract_Programing_C++.md#user-content-2-合约发布过程)
  - [合约调用过程](./ChainMaker_Contract_Programing_C++.md#user-content-3-合约调用过程)
  - [C++ SDK API描述](./ChainMaker_Contract_Programing_C++.md#user-content-4-c-sdk-api描述)
  

Rust开发手册

- [ChainMaker - 智能合约 - Rust开发手册](ChainMaker_Contract_Programing_Rust.md)
  - [合约编写流程](./ChainMaker_Contract_Programing_Rust.md#user-content-1-合约编写流程)
    - [示例代码说明](./ChainMaker_Contract_Programing_Rust.md#user-content-13-示例代码说明)
    - [代码编写规则](./ChainMaker_Contract_Programing_Rust.md#user-content-14-代码编写规则)
    - [编译说明](./ChainMaker_Contract_Programing_Rust.md#user-content-15-编译说明)
  - [合约发布过程](./ChainMaker_Contract_Programing_Rust.md#user-content-2-合约发布过程)
  - [合约调用过程](./ChainMaker_Contract_Programing_Rust.md#user-content-3-合约调用过程)
  - [Rust SDK API描述](./ChainMaker_Contract_Programing_Rust.md#user-content-4-rust-sdk-api描述)
  

智能合约预编译依赖

- [ChainMaker - 智能合约预编译依赖](./ChainMaker_Contract_Compile_Dependence.md)
  - [wamser](./ChainMaker_Contract_Compile_Dependence.md#user-content-wamser)
  - [wxvm](./ChainMaker_Contract_Compile_Dependence.md#user-content-wxvm)
  



**运维类：**

部署手册

- [ChainMaker - 部署手册](./ChainMaker_Deploy_Manual.md)
  - [环境要求](./ChainMaker_Deploy_Manual.md#user-content-1-环境要求)
  - [部署方式](./ChainMaker_Deploy_Manual.md#user-content-2-部署方式)
  - [快速部署（SOLO模式）](./ChainMaker_Deploy_Manual.md#user-content-3-快速部署solo模式)
  - [快速部署（4节点TBFT共识）](./ChainMaker_Deploy_Manual.md#user-content-4-快速部署4节点tbft共识)
  

操作手册

- [ChainMaker - 操作手册](./ChainMaker_Operation_Manual.md)
  - [脚本说明](./ChainMaker_Operation_Manual.md#user-content-1-脚本说明)
  - [部署路径说明](./ChainMaker_Operation_Manual.md#user-content-2-部署路径说明)
  - [配置说明](./ChainMaker_Operation_Manual.md#user-content-3-配置说明)
    - [节点配置chainmaker.yml](./ChainMaker_Operation_Manual.md#user-content-31-节点配置chainmakeryml)
    - [链配置bcN.yml](./ChainMaker_Operation_Manual.md#user-content-32-链配置bcyml)
  - [常用日志说明](./ChainMaker_Operation_Manual.md#user-content-4-常用日志说明)
    - [正常日志](./ChainMaker_Operation_Manual.md#user-content-41-正常日志)
    - [错误日志](./ChainMaker_Operation_Manual.md#user-content-42-错误日志)
    - [故障日志](./ChainMaker_Operation_Manual.md#user-content-43-故障日志)
  - [状态码说明](./ChainMaker_Operation_Manual.md#user-content-5-状态码说明)
  

运维手册

- [ChainMaker - 运维手册](./ChainMaker_Maintenance_Manual.md)
  - [链上配置变更](./ChainMaker_Maintenance_Manual.md#user-content-1-链上配置变更)
  - [添加共识节点](./ChainMaker_Maintenance_Manual.md#user-content-3-添加共识节点)
  - [移除共识节点](./ChainMaker_Maintenance_Manual.md#user-content-4-移除共识节点)
  - [添加同步节点](./ChainMaker_Maintenance_Manual.md#user-content-5-添加同步节点)
  - [移除同步节点](./ChainMaker_Maintenance_Manual.md#user-content-6-移除同步节点)
  

pprof性能分析工具

- [ChainMaker - pprof性能分析工具](ChainMaker_PPROF_Manual.md)# test
