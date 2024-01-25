## cast calldata-decode

### 名称

cast-calldata-decode - 解码 ABI 编码的输入数据。

### 概要

``cast calldata-decode`` [*选项*] *sig* *calldata*

### 描述

解码 ABI 编码的输入数据。

签名（*sig*）是形式为 `<函数名称>(<类型...>)` 的片段。

### 选项

{{#include common-options.md}}

### 示例

1. 解码 `transfer` 调用的输入数据：
    ```sh
    cast calldata-decode "transfer(address,uint256)" \
      0xa9059cbb000000000000000000000000e78388b4ce79068e89bf8aa7f218ef6b9ab0e9d0000000000000000000000000000000000000000000000000008a8e4b1a3d8000
    ```

### 另请参阅

[cast](./cast.md), [cast abi-decode](./cast-abi-decode.md)