# 长安链 · ChainMaker Contract Manual

[TOC]

## 智能合约介绍

智能合约是一种计算机程序或交易协议，记录了交易条款信息、事件、行为，旨在减少对可信中间人的需求、仲裁和执行成本。在ChanMaker上，用户可以通过高级语言（C++、Go、Rust、JS）来编写智能合约，经过编译后，以WASM、EVM字节码的形式存储在区块链中。用户可以通过发送交易来触发执行智能合约中的代码。

## 智能合约SDK

用户通过高级语言编写的智能合约一般情况而言，都需要存取区块链上的数据、API支持，ChainMaker为不同的高级语言提供了不同的SDK。当然，这些SDK提供的基本能力是相同的，包括读取数据、写入数据、查询区块链的一些状态等。

不同语言的SDK受限于语言本身特性和编译器的支撑能力，比如go语言支持函数同时返回多个数据，而tinygo编译器对垃圾回收支持存在缺陷，加上区块链系统本身为智能合约提供的运行内存大小受限、调用栈深度受限，用户编写合约时，需要注意这些特性。

## 智能合约生命周期管理

>  交易发送参考： [使用GO SDK发送交易](../用户手册/sdk_doc/chainmaker-go-sdk.md#useContractInterface)   [使用JAVA SDK发送交易](../用户手册/sdk_doc/chainmaker-java-sdk.md#useContractInterface)  



### 合约部署

用户编写完成智能合约后，经过编译器编译为字节码，需要通过发送交易的形式部署到区块链上。发送的交易将被共识节点和同步节点接收和处理，在校验完成各项参数后，字节码将被存储在区块链数据库中。

在校验参数的过程中，如果下列校验出错，将把执行的错误信息记录在交易的执行结果中：

- 同一条链上不允许存在重名的合约
- 字节码不能为空
- 指定的智能合约执行引擎必须有效
- 版本信息不能为空

随后将调用执行合约的初始化方法：

- 对于WASM而言，将调用**init_contract()**方法，用户必须提供导出的**init_contract()**方法
- 对于EVM而言，将调用构造方法

### 合约升级

ChainMaker支持对基于WASM和EVM的字节码进行升级

- 对于WASM而言，将调用**upgrade()**方法，用户必须提供导出的**upgrade()**方法
- 对于EVM而言，并不会调用任何方法，只是单纯更新字节码

合约升级也同样需要校验参数，如果下列校验出错，将把执行的错误信息记录在交易的执行结果中：

- 合约必须已经被部署成功
- 字节码不能为空
- 版本信息不能为空

### 合约冻结

如果用户需要对已经部署在ChainMaker的智能合约进行冻结操作，需要发送冻结类型的交易，并指定合约名称，就可以冻结合约。被冻结的合约不允许被用户调用执行。

### 合约解冻

用户也可以解冻已经被冻结的合约。

### 合约注销

用户也可以注销已经被安装的合约，合约一旦被注销，将永远无法再次对合约发起任何操作。



## 智能合约Sample

### 存证

**Go（TinyGo）语言版本**

```go
package main

// 安装合约时会执行此方法，必须
//export init_contract
func init_contract() {
    // 安装时的业务逻辑，可为空

}
// 升级合约时会执行此方法，必须
//export upgrade
func upgrade() {
    // 升级时的业务逻辑，可为空

}

//export save
func save() {
	// 获取参数
	txId, _ := GetTxId()
	time, _ := Arg("time")
	fileHash, _ := Arg("file_hash")
	fileName, _ := Arg("file_name")

	// 组装
	stone := make(map[string]string,4)
	stone["txId"]=txId
	stone["time"]=time
	stone["fileHash"]=fileHash
	stone["fileName"]=fileName

	// 序列化为json bytes
	bytes,err := Marshal(stone)
	if err!=nil {
		LogMessage("marshal fail")
		ErrorResult("save fail. marshal fail")
		return
	}

	// 存储数据
	PutState("fact", fileHash, string(bytes))
	// 返回结果
	SuccessResult("ok")
}

//export find_by_file_hash
func findByFileHash() {
	// 获取参数
	fileHash, _ := Arg("file_hash")
	// 查询
	if result, resultCode := GetState("fact", fileHash); resultCode != SUCCESS {
		// 返回结果
		ErrorResult("failed to call get_state.")
	} else {
		// 记录日志
		LogMessage("get val:" + result)
		// 返回结果
		SuccessResult(result)
	}
}

// 编译wasm入口
func main() {

}

```



**rust语言版本**

```rust
use crate::sim_context;
// 安装合约时会执行此方法，必须
#[no_mangle]
pub extern "C" fn init_contract() {
    sim_context::log("init_contract");
    // 安装时的业务逻辑，可为空
}

// 升级合约时会执行此方法，必须
#[no_mangle]
pub extern "C" fn upgrade() {
    sim_context::log("upgrade");
    // 升级时的业务逻辑，可为空
}

// 保存
#[no_mangle]
pub extern "C" fn save() {
    // 获取上下文
    let ctx = &mut sim_context::get_sim_context();

    // 获取传入参数
    let file_hash = ctx.arg_default_blank("file_hash");
    let file_name = ctx.arg_default_blank("file_name");

    // 构建json object
    let object = object! {
        file_hash: file_hash.clone(),
        file_name: file_name,
    };
    // 存储
    ctx.put_state("fact", &file_hash, object.to_string().as_bytes());
}

// 查询
#[no_mangle]
pub extern "C" fn find_by_file_hash() {
    // 获取上下文
    let ctx = &mut sim_context::get_sim_context();
    
    // 获取传入参数
    let file_hash = ctx.arg_default_blank("file_hash");
    
    // 校验参数
    if file_hash.len() == 0 {
        ctx.log("file_hash is null");
        ctx.ok("");
        return;
    }

    // 查询
    let (fact_vec, code) = ctx.get_state("fact", &file_hash);
    
    // 校验返回结果
    if code != SUCCESS_CODE {
        ctx.log("get_state fail");
        ctx.error("get_state fail");
        return;
    }
    if fact_vec.len() == 0 {
        ctx.log("None");
        ctx.ok("");
        return;
    }
    
    // 序列化
    let fact_str = std::str::from_utf8(&fact_vec).unwrap();

    // 打印日志
    ctx.log(fact_str);
    // 返回查询结果
    ctx.ok(fact_str);
}

```



**C++语言版本**

```c++
#include "chainmaker/chainmaker.h"

using namespace chainmaker;

class Counter : public Contract {
public:
    void init_contract() {}
    void upgrade() {}
    // 保存
    void save() {
        // 获取SDK 接口上下文
        Context* ctx = context();
        // 定义变量
        std::string time;
        std::string file_hash;
        std::string file_name;
        std::string tx_id;
		    // 获取参数
        ctx->arg("time", time);
        ctx->arg("file_hash", file_hash);
        ctx->arg("file_name", file_name);
        ctx->arg("tx_id", tx_id);
		    // 存储数据
        ctx->put_object("fact"+ file_hash,  tx_id+" "+time+" "+file_hash+" "+file_name);
        // 记录日志
        ctx->log("call save() result:" + tx_id+" "+time+" "+file_hash+" "+file_name);
        // 返回结果
        ctx->success(tx_id+" "+time+" "+file_hash+" "+file_name);
    }

    // 查询
    void find_by_file_hash() {
        // 获取SDK 接口上下文
    	  Context* ctx = context();

		    // 获取参数
        std::string file_hash;
        ctx->arg("file_hash", file_hash);
		
        // 查询数据
    	  std::string value;
        ctx->get_object("fact"+ file_hash, &value);
        // 记录日志
        ctx->log("call find_by_file_hash()-" + file_hash + ",result:" + value);
        // 返回结果
        ctx->success(value);
    }

};

// 在创建本合约时, 调用一次init方法. ChainMaker不允许用户直接调用该方法.
WASM_EXPORT void init_contract() {
    Counter counter;
    counter.init_contract();
}

// 在升级本合约时, 对于每一个升级的版本调用一次upgrade方法. ChainMaker不允许用户直接调用该方法.
WASM_EXPORT void upgrade() {
    Counter counter;
    counter.init();
}

WASM_EXPORT void save() {
    Counter counter;
    counter.save();
}

WASM_EXPORT void find_by_file_hash() {
    Counter counter;
    counter.find_by_file_hash();
}
```



### 转账

**Rust语言版本**

```rust
/// ------user-----
/// fn init_contract(issue_limit, total_supply, manager_pk)
/// fn upgrade(issue_limit, total_supply, manager_pk)
/// fn register()
/// fn issue_amount(to, amount)
/// fn transfer(to, amount)
/// fn approve(spender, amount)
/// fn transfer_from(from, to, amount)
/// ------query-----
/// fn name()
/// fn symbol()
/// fn total_supply() -> total_supply
/// fn issued_amount() -> amount
/// fn balance_of() -> amount
/// fn allowance() -> amount
/// fn query_address() -> addr
/// fn query_other_address(pub_key)->addr
use crate::sim_context;
use sim_context::SimContext;

const VERSION: &str = "1.0.0";
const NAME: &str = "asset_management";
const SYMBOL: &str = "erc20";

/// get contract version
#[no_mangle]
pub extern "C" fn get_version() {
    let ctx = &mut sim_context::get_sim_context();
    ctx.ok(VERSION.as_bytes());
    ctx.log(VERSION);
}

/// get name
#[no_mangle]
pub extern "C" fn name() {
    let ctx = &mut sim_context::get_sim_context();
    ctx.ok(NAME.as_bytes());
}

/// get symbol
#[no_mangle]
pub extern "C" fn symbol() {
    let ctx = &mut sim_context::get_sim_context();
    ctx.ok(SYMBOL.as_bytes());
}

/// init
///
/// @param issue_limit 发行资产单笔限制, > 100
/// @param total_supply 发行资产总额, >1000
/// @param balance 合约创建人的初始资产
/// @param manager_pk 管理员公钥，以逗号','分割，不可超过5个。管理员具有分配资产（发钱）的权利。当无可用资产时，不可再发。
///
/// @ok creator address
#[no_mangle]
pub extern "C" fn init_contract() {
    sim_context::log("init start.");
    let ctx = &mut sim_context::get_sim_context();

    let pub_key_creator = ctx.get_creator_pub_key();
    let address = calc_address(&pub_key_creator);
    // get param
    let issue_limit_str = ctx.arg_default_blank("issue_limit");
    let total_supply_str = ctx.arg_default_blank("total_supply");
    let mut manager_pk_str = ctx.arg_default_blank("manager_pk");

    // verify num
    let issue_limit = match parse_amount(&issue_limit_str) {
        Ok(val) => val,
        Err(msg) => {
            ctx.error(&msg);
            return;
        }
    };
    if issue_limit < 100 {
        ctx.error("issue_limit less 100.");
        return;
    }
    let total_supply = match parse_amount(&total_supply_str) {
        Ok(val) => val,
        Err(msg) => {
            ctx.error(&msg);
            return;
        }
    };
    if total_supply < 1000 {
        ctx.error("total_supply less 1000.");
        return;
    }

    // verify pk
    if manager_pk_str.len() == 0 {
        manager_pk_str = pub_key_creator;
    }
    let pks = manager_pk_str.split(",");
    let mut count = 0;
    for item in pks {
        if !is_pk(item) {
            ctx.error("manager_pk error");
            return;
        }
        count += 1;
        // register for manager
        let address_item = calc_address(&item); // hash160
        let (balance_item, _) = ctx.get_state(&address_item, "balance");
        if balance_item.len() == 0 {
            ctx.put_state(&address_item, "balance", "0".as_bytes());
        }
    }
    if count > 5 {
        ctx.error("manager_pk too much ");
        return;
    }

    let mut issued_amount = 0;
    let (data, r) = ctx.get_state("init", "issued_amount");
    if r == sim_context::SUCCESS_CODE && data.len() > 0 {
        issued_amount = get_u32_from_str_byte(&data);
    }
    if issued_amount > total_supply {
        ctx.error("issued_amount more than total_supply");
        return;
    }

    // save data
    ctx.put_state("init", "total_supply", total_supply_str.as_bytes());
    ctx.put_state("init", "manager_pk", manager_pk_str.as_bytes());
    ctx.put_state("init", "issue_limit", issue_limit_str.as_bytes());
    ctx.put_state(
        "init",
        "issued_amount",
        issued_amount.to_string().as_bytes(),
    );
    // log
    ctx.log(&format!(
        "init: successed. Address[{:?}] . Total_supply is {}. Issue limit is {}",
        address, total_supply, issue_limit
    ));
    ctx.ok(address.as_bytes());
}

/// upgrade
#[no_mangle]
pub extern "C" fn upgrade() {
    sim_context::log("upgrade");
    init_contract();
}

/// register 开户
///
/// 开户后，余额为0。每个证书只能开户一次
/// @ok addr
#[no_mangle]
pub extern "C" fn register() {
    let ctx = &mut sim_context::get_sim_context();

    let pub_key = ctx.get_sender_pub_key();
    let address_a = calc_address(&pub_key); // hash160
    let (balance_a, r) = ctx.get_state(&address_a, "balance");
    if r != sim_context::SUCCESS_CODE {
        ctx.error("Register fail");
        return;
    }
    if balance_a.len() != 0 {
        ctx.error("Already exists.");
        return;
    }
    ctx.put_state(&address_a, "balance", "0".as_bytes());
    ctx.ok(address_a.as_bytes());
    ctx.log(&format!("[{:?}] register success", address_a));
}

/// issue_amount 发钱
///
/// 只有管理员有权限发钱
///
/// @param to 收账人地址，开户时返回地址，或者可查询地址
/// @param amount 金额，正整数
#[no_mangle]
pub extern "C" fn issue_amount() {
    let ctx = &mut sim_context::get_sim_context();
    let sender_pub_key = ctx.get_sender_pub_key();
    let manager_pub_key = get_manager_pk(ctx).unwrap();
    if !manager_pub_key.contains(&sender_pub_key) {
        ctx.error("No permission.");
        return;
    }
    // let creator_pub_key = ctx.get_creator_pub_key();
    // if creator_pub_key != sender_pub_key {
    //     ctx.error("No permission.");
    //     return;
    // }

    let issue_limit = get_issue_limit(ctx).unwrap();
    let address_to = ctx.arg_default_blank("to");
    let amount = ctx.arg_default_blank("amount");

    let amount = match parse_amount(&amount) {
        Ok(val) => val,
        Err(msg) => {
            ctx.error(&msg);
            return;
        }
    };
    if amount > issue_limit {
        ctx.error(&format!(
            "amount[{}] more than issue limit {}.",
            amount, issue_limit
        ));
        return;
    }

    let balance_to = match get_balance(ctx, &address_to) {
        Ok(val) => val,
        Err(msg) => {
            ctx.error(&msg);
            return;
        }
    };

    let balance_to_new = balance_to + amount;
    let supply = get_total_supply(ctx).unwrap();
    let mut issued_amount = get_issued_amount(ctx).unwrap();
    issued_amount = issued_amount + amount;
    if issued_amount > supply {
        ctx.error(&format!(
            "Sorry, fund pool exceeds upper limit. Supply is {}. issued_amount is {}",
            supply, issued_amount
        ));
        return;
    }

    ctx.put_state(
        &address_to,
        "balance",
        balance_to_new.to_string().as_bytes(),
    );
    ctx.put_state(
        "init",
        "issued_amount",
        issued_amount.to_string().as_bytes(),
    );
    ctx.log(&format!(
        "issue_amount: issue [{:?}] [{}], issued amount is [{}], supply is {}",
        address_to, amount, issued_amount, supply
    ));
}

/// transfer 转账
///
/// @param to 收账人地址，开户时返回地址，或者可查询地址
/// @param amount 金额，正整数
/// @ok "ok"
#[no_mangle]
pub extern "C" fn transfer() {
    let ctx = &mut sim_context::get_sim_context();

    let pub_key = ctx.get_sender_pub_key();
    let from = calc_address(&pub_key); // hash160
    let to = ctx.arg_default_blank("to");
    let amount = ctx.arg_default_blank("amount");

    if from == to {
        ctx.error("You can't transfer to yourself");
        return;
    }

    let amount = match parse_amount(&amount) {
        Ok(val) => val,
        Err(msg) => {
            ctx.error(&msg);
            return;
        }
    };

    let mut balance_from = match get_balance(ctx, &from) {
        Ok(val) => val,
        Err(msg) => {
            ctx.error(&msg);
            return;
        }
    };

    let mut balance_to = match get_balance(ctx, &to) {
        Ok(val) => val,
        Err(msg) => {
            ctx.error(&msg);
            return;
        }
    };
    // 余额不足
    if amount > balance_from {
        ctx.error("The balance of from not enough.");
        return;
    }
    balance_from = balance_from - amount;
    balance_to = balance_to + amount;
    ctx.put_state(&to, "balance", balance_to.to_string().as_bytes());
    ctx.put_state(&from, "balance", balance_from.to_string().as_bytes());
    ctx.log(&format!("transfer: [{:?}] to [{:?}] {}", from, to, amount));
    ctx.ok("ok".as_bytes());
}

/// approve 授权spender一定数额
///
/// @param  spender 花钱的人
/// @param  amount  数额
#[no_mangle]
pub extern "C" fn approve() {
    let ctx = &mut sim_context::get_sim_context();
    let address_from = ctx.get_sender_pub_key();
    let address_from = calc_address(&address_from);
    let address_spender = ctx.arg_default_blank("spender");
    let amount = ctx.arg_default_blank("amount");

    if !is_addr(&address_spender) {
        ctx.error("address error");
        return;
    }

    let amount = match parse_amount(&amount) {
        Ok(val) => val,
        Err(msg) => {
            ctx.error(&msg);
            return;
        }
    };

    let _ = match get_balance(ctx, &address_spender) {
        Ok(val) => val,
        Err(msg) => {
            ctx.error(&msg);
            return;
        }
    };

    ctx.put_state(
        &address_from,
        &address_spender,
        amount.to_string().as_bytes(),
    );
    ctx.log(&format!(
        "approve success: from {:?} spender {:?} amount {}",
        address_from, address_spender, amount
    ))
}

/// 代转账
///
/// @param from 付款人地址
/// @param to 收账人地址，开户时返回地址，或者可查询地址
/// @param amount 金额，正整数
/// @ok "ok"
#[no_mangle]
pub extern "C" fn transfer_from() {
    let ctx = &mut sim_context::get_sim_context();

    let pub_key = ctx.get_sender_pub_key();
    let address_my = calc_address(&pub_key);
    let from = ctx.arg_default_blank("from");
    let to = ctx.arg_default_blank("to");
    let amount = ctx.arg_default_blank("amount");

    let mut balance_to = match get_balance(ctx, &to) {
        Ok(val) => val,
        Err(msg) => {
            ctx.error(&msg);
            return;
        }
    };
    let mut balance_from = match get_balance(ctx, &from) {
        Ok(val) => val,
        Err(msg) => {
            ctx.error(&msg);
            return;
        }
    };
    let amount = match parse_amount(&amount) {
        Ok(val) => val,
        Err(msg) => {
            ctx.error(&msg);
            return;
        }
    };
    let mut balance_allowance = match get_allowance(ctx, &from, &address_my) {
        Ok(val) => val,
        Err(_) => {
            ctx.error("You need to add allowance from_to");
            return;
        }
    };
    // 授权金额不足
    if amount > balance_allowance {
        ctx.error("The allowance of from_to not enough.");
        return;
    }
    // 余额不足
    if amount > balance_from {
        ctx.error("The balance of from not enough.");
        return;
    }

    balance_allowance = balance_allowance - amount;
    balance_to = balance_to + amount;
    balance_from = balance_from - amount;
    ctx.put_state(&from, &address_my, balance_allowance.to_string().as_bytes());
    ctx.put_state(&to, "balance", balance_to.to_string().as_bytes());
    ctx.put_state(&from, "balance", balance_from.to_string().as_bytes());
    ctx.log(&format!(
        "transferFrom: [{:?}] to [{:?}] {}",
        from, to, amount
    ));
    ctx.ok("ok".as_bytes());
}
/// total_supply 当前合约总金额
///
/// @ok amount
#[no_mangle]
pub extern "C" fn total_supply() {
    let ctx = &mut sim_context::get_sim_context();

    let (supply, _) = ctx.get_state("init", "total_supply");
    let supply = std::str::from_utf8(&supply).unwrap();
    ctx.ok(supply.as_bytes());
    ctx.log(&format!("total supply is [{:?}] ", supply));
}

/// issued_amount 当前合约已发金额
///
/// @ok amount
#[no_mangle]
pub extern "C" fn issued_amount() {
    let ctx = &mut sim_context::get_sim_context();
    
    let (data, _) = ctx.get_state("init", "issued_amount");
    let data = std::str::from_utf8(&data).unwrap();
    ctx.ok(data.as_bytes());
    ctx.log(&format!("total issued is [{:?}] ", data));
}

/// balance_of 获取自己账户余额
///
/// @param owner
/// @ok balance
#[no_mangle]
pub extern "C" fn balance_of() {
    let ctx = &mut sim_context::get_sim_context();

    let address = ctx.arg_default_blank("owner");
    let balance = match get_balance(ctx, &address) {
        Ok(val) => val,
        Err(msg) => {
            ctx.error(&msg);
            return;
        }
    };

    ctx.ok(balance.to_string().as_bytes());
    ctx.log(&format!(
        "[{:?}] balance is [{:?}] ",
        address,
        balance.to_string()
    ));
}

/// 获取授权限额
///
/// @param spender 被授权人
/// @param owner 授权人
#[no_mangle]
pub extern "C" fn allowance() {
    let ctx = &mut sim_context::get_sim_context();
    ctx.log("allowance");

    let to = ctx.arg_default_blank("spender");
    let from = ctx.arg_default_blank("owner");
    let _ = match get_balance(ctx, &to) {
        Ok(val) => val,
        Err(msg) => {
            ctx.error(&msg);
            return;
        }
    };
    let _ = match get_balance(ctx, &from) {
        Ok(val) => val,
        Err(msg) => {
            ctx.error(&msg);
            return;
        }
    };
    let amount = match get_allowance(ctx, &from, &to) {
        Ok(val) => val,
        Err(msg) => {
            ctx.error(&msg);
            return;
        }
    };
    ctx.ok(amount.to_string().as_bytes());
}

/// query_address 查询自己的钱包地址
///
/// @ok  address bytes
#[no_mangle]
pub extern "C" fn query_address() {
    let ctx = &mut sim_context::get_sim_context();
    ctx.log("query_address");

    let pub_key = ctx.get_sender_pub_key();
    ctx.log(&format!("[{:?}] pk", pub_key));
    let address = calc_address(&pub_key); // hash160
    ctx.log(&format!("[{:?}] address", address));
    ctx.ok(address.as_bytes());
    ctx.log(&format!("[{:?}] address is [{:?}]", pub_key, address));
}

/// query_address 查询他人的钱包地址
///
/// @param pub_key 公钥
/// @ok    address bytes
#[no_mangle]
pub extern "C" fn query_other_address() {
    let ctx = &mut sim_context::get_sim_context();

    let pub_key = ctx.arg_default_blank("pub_key");
    if !is_pk(&pub_key) {
        ctx.error("pub_key error.");
        return;
    }
    let address = calc_address(&pub_key);
    let _ = match get_balance(ctx, &address) {
        Ok(val) => val,
        Err(msg) => {
            ctx.error(&msg);
            return;
        }
    };
    ctx.ok(address.as_bytes());
    ctx.log(&format!("pub key [{:?}] address [{:?}]", pub_key, address));
}

/// 从链上获取余额并校验
///
/// ERR：查询失败、未注册
fn get_balance(ctx: &mut SimContext, address: &str) -> Result<u32, String> {
    if address.len() == 0 {
        return Err("miss address".to_string());
    }
    let (balance, r) = ctx.get_state(address, "balance");
    if r != sim_context::SUCCESS_CODE {
        return Err(format!("[{:?}] get balance fail", address));
    }
    if balance.len() == 0 {
        return Err(format!("[{:?}] not registered", address));
    }
    let balance = std::str::from_utf8(&balance).unwrap();
    let balance = balance.parse::<u32>().unwrap();
    Ok(balance)
}

/// 从链上获取单笔资产发行上限
fn get_issue_limit(ctx: &mut SimContext) -> Result<u32, String> {
    let (data, r) = ctx.get_state("init", "issue_limit");
    if r != sim_context::SUCCESS_CODE || data.len() == 0 {
        return Err("get_issue_limit fail".to_string());
    }
    let issue_limit_str = std::str::from_utf8(&data).unwrap();
    Ok(issue_limit_str.parse::<u32>().unwrap())
}

/// 从链上获取已发行资产
fn get_issued_amount(ctx: &mut SimContext) -> Result<u32, String> {
    let (data, r) = ctx.get_state("init", "issued_amount");
    if r != sim_context::SUCCESS_CODE || data.len() == 0 {
        return Err("get_issued_amount fail".to_string());
    }
    let issued_amount = std::str::from_utf8(&data).unwrap();
    Ok(issued_amount.parse::<u32>().unwrap())
}

/// 从链上获取资产总额
fn get_total_supply(ctx: &mut SimContext) -> Result<u32, String> {
    let (data, r) = ctx.get_state("init", "total_supply");
    if r != sim_context::SUCCESS_CODE || data.len() == 0 {
        return Err("get_total_supply fail".to_string());
    }
    let total_supply = std::str::from_utf8(&data).unwrap();
    Ok(total_supply.parse::<u32>().unwrap())
}

/// 获取管理员公钥
fn get_manager_pk(ctx: &mut SimContext) -> Result<String, String> {
    let (data, r) = ctx.get_state("init", "manager_pk");
    if r != sim_context::SUCCESS_CODE || data.len() == 0 {
        return Err("get_manager_pk fail".to_string());
    }
    let manager_pk = std::str::from_utf8(&data).unwrap();
    Ok(manager_pk.to_string())
}

/// 获取授权总额
fn get_allowance(ctx: &mut SimContext, from: &str, to: &str) -> Result<u32, String> {
    if from.len() == 0 || to.len() == 0 {
        return Err("miss address".to_string());
    }
    let (data, r) = ctx.get_state(from, to);
    if r != sim_context::SUCCESS_CODE || data.len() == 0 {
        return Err("get_allowance fail".to_string());
    }
    if data.len() == 0 {
        return Err(format!("[{:?}] not approved {:?}", from, to));
    }
    let amount = std::str::from_utf8(&data).unwrap();
    Ok(amount.parse::<u32>().unwrap())
}

/// 校验是否是公钥格式
fn is_pk(pk: &str) -> bool {
    if pk.len() == 0 || pk.len() > 64 {
        return false;
    }
    true
}
/// 校验是否是地址格式
fn is_addr(addr: &str) -> bool {
    if addr.len() == 0 || addr.len() > 64 {
        return false;
    }
    return true;
}

/// 解析金额为正整数
///
/// 为空、不是数字，非整数，超过int 则返回错误信息
fn parse_amount(amount: &str) -> Result<u32, String> {
    if amount.len() == 0 {
        return Err("amount is null".to_string());
    }
    // 引入Regex包会消耗大量gas，故自己遍历
    // let amount_reg = Regex::new(r"^\d+$").unwrap();
    // if !amount_reg.is_match(&amount) {
    //     return Err(format!("amount is {} not num", amount));
    // }
    for elem in amount.chars() {
        if '0' <= elem && '9' >= elem {
            continue;
        }
        if '-' == elem {
            return Err(format!("amount[{}] less than 0. ", amount));
        }
        return Err(format!("amount[{}] is  not number. ", amount));
    }
    let amount = match amount.parse::<u32>() {
        Ok(num) => num,
        Err(err) => {
            return Err(format!(
                "Maybe, the amount[{}] is too large.{:?}",
                amount, err
            ));
        }
    };
    Ok(amount)
}

// 将&[u8] byte转换为u32
fn get_u32_from_str_byte(amount: &[u8]) -> u32 {
    let amount = std::str::from_utf8(amount).unwrap();
    amount.parse::<u32>().unwrap()
}

// 根据公钥计算地址
// fn cal_address(pub_key: &str) -> String {
//     use data_encoding::HEXUPPER;
//     use ring::digest::{Context, SHA256};
//     let data = pub_key.as_bytes();

//     let mut context = Context::new(&SHA256);
//     context.update(data);
//     let digest = context.finish();

//     let hash = digest.as_ref();
//     let hex = HEXUPPER.encode(&hash[..160]);
//     hex
// }

// 根据公钥计算地址
fn calc_address(pub_key: &str) -> String {
    return pub_key.to_string();
}

```



## 编译智能合约

ChainMaker支持通过Docker的方式编译和运行智能合约

**Go（TinyGo）编译和运行**

拉取镜像
```
docker pull huzhenyuan/chainmaker-go-contract:1.0.0
docker run -it --name chainmaker-go-contract -v <WORK_DIR>:/home chainmaker-go-contract bash
```

编译合约
```

# cd /home/
# tar xvf /data/contract_go_template.tar.gz
# cd contract_go
# sh build.sh
```

运行合约
```
# gasm main.wasm save time 20210304 file_hash 12345678 file_name a.txt
```

**C++编译和运行**

拉取镜像
```
docker pull huzhenyuan/chainmaker-cpp-contract:1.0.0
docker run -it --name chainmaker-cpp-contract -v <WORK_DIR>:/home chainmaker-cpp-contract bash
```

编译合约
```

# cd /home/
# tar xvf /data/contract_cpp_template.tar.gz
# cd contract_cpp
# emmake make
```

运行合约
```
# wxvm main.wasm divide num1 100 num2 8
```


**Rust编译和运行**

拉取镜像
```
docker pull huzhenyuan/chainmaker-rust-contract:1.0.0
docker run -it --name chainmaker-rust-contract -v <WORK_DIR>:/home chainmaker-rust-contract bash
```

编译合约
```
# cd /home/
# tar xvf /data/contract_rust_template.tar.gz
# cd contract_rust
# wasm-pack build
```

运行合约
```
# wasmer main.wasm save time 20210304 file_hash 12345678 file_name a.txt
```

## 约束条件和已知问题

- 安装CPP智能合约时，要求共识节点、非共识节点必须安装GCC。

- TinyGo对wasm的支持不太完善，对内存逃逸分析、GC等方面有不足之处，比较容易造成栈溢出。在开发合约时，应尽可能减少循环、内存申请等业务逻辑，使变量的栈内存地址在64K以内。



