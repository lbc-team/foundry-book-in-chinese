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

### 请参阅

[cast](./cast.md), [cast nonce](./cast-nonce.md)
