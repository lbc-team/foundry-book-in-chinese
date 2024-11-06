## cast send

### 名称

cast-send - 签署并发布一项交易。

### 简介

``cast send`` [*options*] *to* [*sig*] [*args...*]

### 描述

签署并发布一项交易。

目的地（*to*）可以是 ENS 名称或地址。

{{#include sig-description.md}}

### 可选

{{#include ../common/transaction-options.md}}

`--resend`  
&nbsp;&nbsp;&nbsp;&nbsp;重复使用发送账户的最新 nonce。

`--create` *code* [*sig* *args...*]  
&nbsp;&nbsp;&nbsp;&nbsp;通过指定原始字节码来部署合约，以代替指定 *to* 地址。

#### Receipt 选项

`--async`  
`--cast-async`  
&nbsp;&nbsp;&nbsp;&nbsp;如果交易收据还不存在，就不要等它了。 
&nbsp;&nbsp;&nbsp;&nbsp;环境: `CAST_ASYNC`

`-c` *confirmations*  
`--confirmations` *confirmations*  
&nbsp;&nbsp;&nbsp;&nbsp;在退出前等待一定数量的交易确认。默认值：`1`。

{{#include ../common/wallet-options.md}}

`--unlocked`  
&nbsp;&nbsp;&nbsp;&nbsp;通过 `eth_sendTransaction` 使用 `--from` 参数或者 `$ETH_FROM` 作为发送者发送。

{{#include ../common/rpc-options.md}}

{{#include ../common/etherscan-options.md}}

{{#include ../common/display-options.md}}

{{#include common-options.md}}

### 例子

1. 用你的 Ledger 给 Vitalik 发送一些 ether。
    ```sh
    cast send --ledger vitalik.eth --value 0.1ether
    ```

2. 在一个合约上调用 `deposit(address token, uint256 amount)`：
    ```sh
    cast send --ledger 0x... "deposit(address,uint256)" 0x... 1
    ```

3. 调用一个预期输入 `struct` 的函数:

    ```solidity
    contract Test {
        struct MyStruct {
            address addr;
            uint256 amount;
        }
        function myfunction(MyStruct memory t) public pure {}
    }
    ```

    结构体被编码为元组 (请参阅 [struct encoding](../../misc/struct-encoding.md))

    ```sh
    cast send 0x... "myfunction((address,uint256))" "(0x...,1)"
    ```

4. 在交易对象的 `input` 字段中发送带有十六进制数据的交易：
    ```sh
    cast send 0x... 0x68656c6c6f20776f726c64
    ```

5. 签署一个 EIP-7702 授权，并将其附加到来自不同发送者的交易中：
    ```sh
    cast send $(cast az) --private-key <sender-pk> --auth $(cast wallet sign-auth <address> --private-key <delegator-pk>)
    ```

6. 发送一个 EIP-7702 交易，将发送者委托给 `<address>`：
    ```sh
    cast send $(cast az) --auth <address>
    ```


### 请参阅

[cast](./cast.md), [cast call](./cast-call.md), [cast publish](./cast-publish.md), [cast receipt](./cast-receipt.md), [cast mktx](./cast-mktx.md), [struct encoding](../../misc/struct-encoding.md)