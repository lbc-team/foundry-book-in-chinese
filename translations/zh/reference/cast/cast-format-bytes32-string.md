## cast format-bytes32-string

### 名称

cast-format-bytes32-string - 将字符串格式化为 bytes32 编码。

### 概要

``cast format-bytes32-string`` [*选项*] *字符串*

### 描述

将字符串格式化为 bytes32 编码。

请注意，此命令仅用于将 [Solidity 字符串文字](https://docs.soliditylang.org/en/v0.8.16/types.html#string-literals-and-types)格式化为 `bytes32`。如果你想要填充字节字符串，请改用 [to-bytes32](./cast-to-bytes32.md)。

### 选项

{{#include common-options.md}}

### 示例

1. 将字符串 "hello" 转换为 bytes32 十六进制：
    ```sh
    cast format-bytes32-string "hello"
    ```

### 另请参阅

[cast](./cast.md) 