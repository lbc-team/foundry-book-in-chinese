## cast block

### 名称

cast-block - 获取有关区块的信息。

### 概要

``cast block`` [*options*] [*block*]

### 描述

获取有关区块的信息。

指定的 *block* 可以是区块编号，也可以是任何标签：`earliest`、`finalized`、`safe`、`latest` 或 `pending`。默认为 `latest`。

### 选项

`-f` *field*  
`--field` *field*  
&nbsp;&nbsp;&nbsp;&nbsp; 如果指定，则仅获取区块的给定字段。

{{#include ../common/display-options.md}}

#### RPC 选项

{{#include ../common/rpc-url-option.md}}

{{#include common-options.md}}

### 示例

1. 获取最新的区块：
    ```sh
    cast block
    ```

2. 获取`finalized`区块：
    ```sh
    cast block finalized
    ```

3. 获取最新区块的哈希值：
    ```sh
    cast block latest -f hash
    ```

### 参见

[cast](./cast.md), [cast basefee](./cast-basefee.md), [cast age](./cast-age.md)