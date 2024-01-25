## cast from-rlp

### 名称

cast-from-rlp- 解码 RLP 编码的数据。

### 概要

``cast from-rlp`` *data*

### 描述

解码 RLP 编码的数据。

*data* 是一个十六进制字符串，可以选择性地包含 0x 前缀。

### 选项

{{#include common-options.md}}

### 示例

1. 解码 RLP 数据：
    ```sh
    cast from-rlp 0xc481f181f2

    cast from-rlp c481f181f2
    ```