## cast code

### 名称

cast-code - 获取一个合约的字节码。

### 简介

``cast code`` [*options*] *address*

### 描述

获取一个合约的字节码。

合约（*address*）可以是 ENS 的名称或地址。

### 可选

#### Query Options

`-B` *block*  
`--block` *block*  
&nbsp;&nbsp;&nbsp;&nbsp;你想查询的区块高度。

&nbsp;&nbsp;&nbsp;&nbsp;可以是一个区块编号，或任何一个标签: `earliest`, `latest`, `finalized`, `safe` 或者 `pending`.

#### RPC 选项

{{#include ../common/rpc-url-option.md}}

{{#include common-options.md}}

### 例子

1. 获取 WETH 合约的字节码：
    ```sh
    cast code 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2
    ```

### 请参阅

[cast](./cast.md), [cast proof](./cast-proof.md)
