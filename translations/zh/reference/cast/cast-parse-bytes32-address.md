## cast parse-bytes32-address

### 名称

cast-parse-bytes32-address - 从 bytes32 编码中解析出一个经过校验的地址。

### 概要

``cast parse-bytes32-address`` [*options*] *bytes*

### 描述

从其 bytes32 编码表示中解析出一个经过校验的地址。

### 选项

{{#include common-options.md}}

### 例子

1. 将 WETH9 合约地址的 bytes32 编码解析为其地址表示形式：
    ```sh
    cast parse-bytes32-address 0x000000000000000000000000C02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2
    ```

### 参见

[cast](./cast.md) 