## cast to-rlp

### 名称

cast-to-rlp - 将十六进制数据编码为 RLP。

### 概要

``cast to-rlp`` *array*

### 描述

RLP 对十六进制字符串或十六进制字符串的 JSON 数组进行编码。

### 选项

{{#include common-options.md}}

### 例子

1. 编码 RLP 数据：
    ```sh
    cast to-rlp '["0xaa","0xbb","cc"]'
   
    cast to-rlp f0a9     
    ```
