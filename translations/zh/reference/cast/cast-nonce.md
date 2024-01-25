## cast nonce

### 名称

cast-nonce - 获取一个账户的 nonce。

### 简介

``cast nonce`` [*options*] *who*

### 描述

获取一个账户的 nonce。

参数 *who* 可以是一个 ENS 名称或地址。

### 可选

#### 查询选项

`-B` *block*  
`--block` *block*  
&nbsp;&nbsp;&nbsp;&nbsp;你想查询的区块高度。

&nbsp;&nbsp;&nbsp;&nbsp;可以是一个区块编号，或任何一个标签：`earliest`, `latest`, `finalized`, `safe` 或者 `pending`。

#### RPC 选项

{{#include ../common/rpc-url-option.md}}

{{#include common-options.md}}

### 例子

1. 获取 beer.eth 的 nonce：
    ```sh
    cast nonce beer.eth
    ```

### 请参阅

[cast](./cast.md), [cast balance](./cast-balance.md)
