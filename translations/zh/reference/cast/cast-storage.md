## cast storage

### 名称

cast-storage - 获取合约存储槽的原始值或其完整存储布局。

### 概要

``cast storage`` [*选项*] *地址* *槽*

### 描述

获取合约存储槽的原始值。（大于 18446744073709551615（u64::MAX）的槽位置应以十六进制给出。使用 `cast index` 计算映射槽。）

发出槽号以获取完整的存储布局（需要合约在 Etherscan 上经过验证，并且使用的 Solidity 版本大于 0.6.5，或者你必须在具有与部署的字节码匹配的本地合约的 Forge 项目中）。

地址（*address*）可以是 ENS 名称或地址。

### 选项

#### 查询选项

`-B` *block*
`--block` *block*
&nbsp;&nbsp;&nbsp;&nbsp;你要查询的区块高度。

&nbsp;&nbsp;&nbsp;&nbsp;可以是区块号，或任何标签：`earliest`，`finalized`，`safe`，`latest` 或 `pending`。

#### RPC 选项

{{#include ../common/rpc-url-option.md}}

{{#include common-options.md}}

### 示例

1. 获取 WETH 合约上槽 0 的值。
    ```sh
    cast storage 0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2 0
    ```

2. 获取 Milady 合约的完整存储布局。
    ```sh
    cast storage 0x5Af0D9827E0c53E4799BB226655A1de152A425a5
    ```

### 参见

[cast](./cast.md), [cast proof](./cast-proof.md)