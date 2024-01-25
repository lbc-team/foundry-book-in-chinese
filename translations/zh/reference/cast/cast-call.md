## cast call

### 名称

cast-call - 在不发布交易的情况下对账户进行调用。

### 概要

``cast call`` [*选项*] *to* *sig* [*args...*]

### 描述

在不发布交易的情况下对账户进行调用。

目标（*to*）可以是 ENS 名称或地址。

{{#include sig-description.md}}

### 选项

`--trace`  
&nbsp;&nbsp;&nbsp;&nbsp;打印交易的跟踪信息。

`--debug`  
&nbsp;&nbsp;&nbsp;&nbsp;使用交易的交互式调试器。需要 `--trace`。

`--verbose`  
&nbsp;&nbsp;&nbsp;&nbsp;打印更详细的跟踪信息。需要 `--trace`。

`--labels <address:label>`  
&nbsp;&nbsp;&nbsp;&nbsp;要应用于跟踪的标签，格式为 `address:label`。需要 `--trace`。

`--evm-version`  
&nbsp;&nbsp;&nbsp;&nbsp;要使用的 EVM 版本。需要 `--trace`。

#### 查询选项

`-B` *block*  
`--block` *block*  
&nbsp;&nbsp;&nbsp;&nbsp;您要查询的区块高度。

&nbsp;&nbsp;&nbsp;&nbsp;可以是区块号，也可以是任何标签：`earliest`、`finalized`、`safe`、`latest`或`pending`。

{{#include ../common/wallet-options.md}}

{{#include ../common/rpc-options.md}}

{{#include ../common/etherscan-options.md}}

{{#include common-options.md}}

### 示例

1. 在 WETH 合约上调用`balanceOf(address)`：

    ```sh
    cast call 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2 \
      "balanceOf(address)(uint256)" 0x...
    ```

2. 在 Tubby Cats NFT 合约上调用`tokenURI(uint256)(string)`：

    ```sh
    export CONTRACT=0xca7ca7bcc765f77339be2d648ba53ce9c8a262bd
    export TOKEN_ID=19938
    cast call $CONTRACT "tokenURI(uint256)(string)" $TOKEN_ID
   ```

3. 在 Uniswap v2 路由器合约上调用``getAmountsOut(uint,address[])``：

    ```sh
   cast call 0x7a250d5630B4cF539739dF2C5dAcb4c659F2488D \
     "getAmountsOut(uint,address[])" 1 "[0x6b...0f,0xc0...c2]"
   ```

### 参见

[cast](./cast.md), [cast send](./cast-send.md), [cast publish](./cast-publish.md), [cast receipt](./cast-receipt.md)