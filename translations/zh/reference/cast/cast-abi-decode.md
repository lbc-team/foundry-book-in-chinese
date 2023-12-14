## cast abi-decode

### 名称

cast-abi-decode - 解码 ABI 编码的输入或输出数据。

### 概要

``cast abi-decode`` [*options*] *sig* *calldata*

### 描述

解码 ABI 编码的输入或输出数据。

默认情况下，该命令将解码输出数据。要解码输入数据，请传递 `--input` 或使用 [`cast calldata-decode`](./cast-calldata-decode.md)。

签名（*sig*）是形式为 `<function name>(<types...>)(<types...>)` 的片段。

### 选项

#### 解码器选项

`-i`  
`--input`  
&nbsp;&nbsp;&nbsp;&nbsp;解码输入数据。

{{#include common-options.md}}

### 示例

1. 解码 `balanceOf` 调用的输出数据：
    ```sh
    cast abi-decode "balanceOf(address)(uint256)" \
      0x000000000000000000000000000000000000000000000000000000000000000a
    ```

2. 解码 `transfer` 调用的输入数据：
    ```sh
    cast abi-decode --input "transfer(address,uint256)" \
      0xa9059cbb000000000000000000000000e78388b4ce79068e89bf8aa7f218ef6b9ab0e9d0000000000000000000000000000000000000000000000000008a8e4b1a3d8000
    ```

### 参见

[cast](./cast.md), [cast calldata-decode](./cast-calldata-decode.md)