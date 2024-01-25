## cast proof

### 名称

cast-proof - 为一个给定的存储槽生成一个存储证明。

### 简介

``cast proof`` [*options*] *address* [*slots...*]

### 描述

为一个给定的存储槽生成一个存储证明。

地址（*address*）可以是 ENS 名称或地址。

显示的输出是一个 JSON 对象，其键值如下。

- `accountProof`: 账户本身的证明
- `address`: 账户的地址
- `balance`: 账户的余额
- `codeHash`: 账户代码的哈希值
- `nonce`: 账户的 nonce
- `storageHash`: 账户存储的哈希值
- `storageProof`: 一个存储证明数组，每个请求的槽位都有一个存储证明
- `storageProof.key`: 槽位
- `storageProof.proof`: 槽位的证明
- `storageProof.value`: 槽位的值

### 可选

#### 查询选项

`-B` *block*  
`--block` *block*  
&nbsp;&nbsp;&nbsp;&nbsp;你想查询的区块高度。

&nbsp;&nbsp;&nbsp;&nbsp;可以是一个区块编号，或任何一个标签： `earliest`, `latest`, `finalized`, `safe` 或者 `pending`.

#### RPC 选项

{{#include ../common/rpc-url-option.md}}

{{#include ../common/display-options.md}}

{{#include common-options.md}}

### 例子

1. 获取 WETH 合约上 0 号存储槽的证明。
    ```sh
    cast proof 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2 0
    ```

### 请参阅

[cast](./cast.md), [cast storage](./cast-storage.md)
