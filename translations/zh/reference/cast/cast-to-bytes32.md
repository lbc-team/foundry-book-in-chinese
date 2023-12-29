## cast to-bytes32

### 名称

cast-to-bytes32 - 将十六进制数据右填充为 32 字节。

### 概要

``cast to-bytes32`` [*选项*] *bytes*

### 描述

将十六进制数据右填充为 32 字节。

请注意，此命令仅用于填充字节字符串。如果您要将 [Solidity 字符串文字](https://docs.soliditylang.org/en/v0.8.16/types.html#string-literals-and-types)格式化为`bytes32`，请改用 [format-bytes32-string](./cast-format-bytes32-string.md)。

### 选项

{{#include common-options.md}}

### 参见

[cast](./cast.md) 
