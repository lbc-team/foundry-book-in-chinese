## cast balance

### 名称

cast-balance - 获取一个账户的余额，单位为 Wei。

### 简介

``cast balance`` [*options*] *who*

### 描述

获取一个账户的余额，单位为 Wei。

参数 *who* 可以是一个 ENS 名称或地址。

### 可选

#### 查询选项

`-B` *block*  
`--block` *block*  
&nbsp;&nbsp;&nbsp;&nbsp;你想查询的区块高度。

&nbsp;&nbsp;&nbsp;&nbsp;可以是一个区块编号，或任何一个标签: `earliest`、`finalized`、`safe`、`latest` 或者 `pending`。

`-e` *ether*  
`--ether` *ether*  
&nbsp;&nbsp;&nbsp;&nbsp; 如果使用了此标志，则余额将以以太显示

#### RPC 选项

{{#include ../common/rpc-url-option.md}}

{{#include common-options.md}}

### 例子

1. 获取 beer.eth 的余额：
    ```sh
    cast balance beer.eth
    ```
2. 使用 RPC URL 获取任意地址的 ERC20 余额
   ```sh
   # 加载 .env 文件中的变量
   source .env

   # 获取币安的 USDT 余额
   cast balance --erc20 0xdAC17F958D2ee523a2206206994597C13D831ec7 0xF977814e90dA44bFA03b6295A0616a897441aceC --rpc-url $MAINNET_RPC_URL

### 请参阅

[cast](./cast.md), [cast nonce](./cast-nonce.md)
