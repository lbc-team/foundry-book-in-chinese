## cast mktx

### 名称

cast-mktx - 构建并签名交易。

### 概述

``cast mktx`` [*选项*] *接收地址* [*签名*] [*参数...*]

### 描述

构建并签名一笔交易，但不发布它。

目标 (*接收地址*) 可以是一个 ENS 名称或一个地址。

{{#include sig-description.md}}

### 选项

{{#include ../common/transaction-options.md}}

`--create` *代码* [*签名* *参数...*]  
&nbsp;&nbsp;&nbsp;&nbsp;通过指定原始字节码来部署合约，而不是指定 *接收地址*。

{{#include ../common/wallet-options-raw.md}}

{{#include ../common/wallet-options-keystore.md}}

{{#include ../common/wallet-options-hardware.md}}

{{#include ../common/rpc-options.md}}

{{#include ../common/etherscan-options.md}}

{{#include common-options.md}}

### 示例

1. 使用你的 Ledger 签名一笔向 Vitalik 发送一些以太的交易：
    ```sh
    cast mktx --ledger vitalik.eth --value 0.1ether
    ```

2. 签名一笔调用合约 `deposit(address token, uint256 amount)` 的交易：
    ```sh
    cast mktx --ledger 0x... "deposit(address,uint256)" 0x... 1
    ```

3. 签名一笔调用期望 `struct` 的函数的交易：

    ```solidity
    contract Test {
        struct MyStruct {
            address addr;
            uint256 amount;
        }
        function myfunction(MyStruct memory t) public pure {}
    }
    ```

    结构体被编码为元组（参见 [结构体编码](../../misc/struct-encoding.md)）

    ```sh
    cast mktx 0x... "myfunction((address,uint256))" "(0x...,1)"
    ```

4. 签名一笔在交易对象的 `input` 字段中包含十六进制数据的交易：
    ```sh
    cast mktx 0x... 0x68656c6c6f20776f726c64
    ```

### 另见

[cast](./cast.md), [cast publish](./cast-publish.md), [cast send](./cast-send.md), [结构体编码](../../misc/struct-encoding.md)