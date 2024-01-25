## cast parse-bytes32-string

### 名称

cast-parse-bytes32-string - 解析 bytes32 编码的字符串。

### 概要

``cast parse-bytes32-string`` [*options*] *bytes*

### 描述

通过将字节解释为 ASCII 字符，从其 bytes32 编码表示中解析 [Solidity 字符串文字](https://docs.soliditylang.org/en/v0.8.16/types.html#string-literals-and-types) 。此命令撤消了 [--format-bytes32-string](./cast-format-bytes32-string.md) 中的编码。

### 选项

{{#include common-options.md}}

### 示例

1. 将“hello”的 bytes32 字符串编码解析回字符串表示形式：
    ```sh
    cast parse-bytes32-string "0x68656c6c6f000000000000000000000000000000000000000000000000000000"
    ```

### 参见

[cast](./cast.md) 
